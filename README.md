[README.md](https://github.com/user-attachments/files/27491053/README.md)
# Agent Security Protocol (ASP)

**Making AI Agent Deployment Safe, Auditable, and Trustworthy**

Version 1.0 — May 2026  
Author: Charlie McKenna  
License: CC BY-SA 4.0

---

## The Problem ASP Solves

AI agents are no longer a research concept. They are deployed at scale — booking meetings, writing code, managing infrastructure, executing financial transactions, and coordinating with other agents. They operate at machine speed, make autonomous decisions, and are fundamentally opaque.

The security tools and practices we rely on today were designed for a world where humans are in the loop. Firewall rules, access control lists, and approval chains all assume a human can review actions in real time. Agents break this assumption. By the time a flagged action reaches a human reviewer, the agent has already executed hundreds more.

No end-to-end operational standard currently covers agent identity, delegated permissions, runtime scope enforcement, behavioral auditability, and forensic feedback as a unified stack. Related efforts exist — NIST AI RMF, OWASP LLM Top 10, MITRE ATLAS — but none address the full agent runtime security problem. **ASP is designed to fill that gap.**

---

## The Core Architectural Principle

**ASP controls the runtime and tool invocation layer. It does not rely on the model to self-police.**

This is the most important idea in the entire protocol. Security properties are enforced by the execution environment — scope boundaries, permission ceilings, audit logging, kill switches, and data flow controls all operate outside the agent's own decision-making. The agent does not need to cooperate with its own governance for ASP to work.

Many agent security failures happen at the boundary between the agent and its tools — not inside the model's reasoning. ASP is specifically designed to secure that boundary.

---

## How ASP Works

ASP is a seven-layer stack. Each layer addresses a specific security concern. Together, they create a comprehensive trust framework. Individually, they provide targeted protection. Organizations adopt the layers that match their risk profile.

| Layer | Name | What It Does |
|-------|------|-------------|
| **0** | **Agent Identity** | Cryptographically verifies every agent. You cannot secure what you cannot identify. Covers the full credential lifecycle: issuance, rotation, suspension, revocation, and expiration. |
| **1** | **Scope Enforcement** | Defines and enforces boundaries — what the agent can access, what actions it can take, and where data is allowed to flow. Deny-by-default: anything not explicitly authorized is forbidden. |
| **2** | **Coherence Monitoring** | Compares what the agent says it's doing to what it actually does. Detects anomalies: resource mismatches, task drift, data flow violations, privilege chaining, and scale mismatches. |
| **3** | **Permission Inheritance** | When agents delegate to sub-agents, the sub-agent's permissions are always a subset of the parent's. Privilege cannot escalate through delegation. If a parent is suspended, all children suspend too. |
| **4** | **Reasoning Document** | Every agent maintains a real-time log of its action rationale — what it understood, what it did, and why. Write-once, stored outside agent control. Does not require disclosure of private chain-of-thought. |
| **5** | **Layered Audit Logs** | One data source, three views: technical trace for engineers, plain language for operators, executive summary on demand. Tamper-evident with cryptographic chaining. |
| **6** | **Forensic Feedback** | Incidents improve the system surgically. Every update tracks whether it reduces, expands, or preserves agent authority. All changes require operator approval and are reversible. |

---

## Key Concepts

### Narrative-Action Coherence Checking

ASP does not claim to read an agent's mind. Instead, it compares *stories to evidence*. The agent's reasoning document tells one story. Its actual actions tell another. When those stories diverge, the system flags it for human review.

This is practical and honest. We don't need to solve the AI interpretability problem to build useful security. We need to detect when an agent's words don't match its behavior — which is the same heuristic humans use to assess trustworthiness in every other context.

### Data Flow Control

Controlling what an agent can access is necessary but insufficient. The critical question is: where can that data go?

An agent with read access to a customer database and write access to an email tool has, in effect, the ability to exfiltrate customer data — even though each permission is reasonable in isolation. ASP's data flow control specifies which sources may flow to which sinks, what transformations are required (summarization, redaction), and which destinations are forbidden. This closes the most realistic exfiltration vector in agent deployments.

### Operator Trust Calibration

Security that's too rigid gets bypassed. ASP starts restrictive and earns its way to efficiency. When an agent encounters a permission boundary, it asks the operator. Over time, operators can *"always allow"* patterns they've reviewed and trust — like approving a known process so it doesn't ask every time.

Standing approvals are scoped, logged, revocable, and expire. Trust is earned through demonstrated behavior, not declared.

### The Flight Recorder Model

ASP's audit log works like an airplane's black box. It's always recording. The agent can't access it, modify it, or delete it. It's stored separately from the agent's execution environment. Each log entry is cryptographically chained to the previous one, so tampering with any part breaks the chain and is immediately detectable.

For compliance with privacy regulations, deletion is possible only through privileged governance procedures that preserve deletion records and integrity metadata — no agent or ordinary operator can do it.

### Emergency Containment

ASP requires a kill switch — an operator-accessible emergency control that immediately suspends agent execution, revokes credentials, freezes all child agents, and preserves forensic state. It operates at the runtime level: the agent does not need to cooperate with its own containment. After containment, the agent cannot resume without explicit operator reauthorization.

### Humans Are Always the Final Arbiter

ASP never removes the human from the decision chain. It surfaces information, flags conflicts, and blocks dangerous actions. But the operator always has final authority. ASP is a decision-support system, not an autonomous governance layer.

---

## Compliance Tiers

Not every agent needs every layer. ASP defines three tiers so organizations can adopt what fits their risk profile and scale up as their agent deployments mature.

| Tier | Layers Included | Best For |
|------|----------------|----------|
| **Tier 1: Foundation** | Identity + Audit Logs | Low-risk deployments, internal tools, proof-of-concept agents. You know who the agent is and what it did. |
| **Tier 2: Controlled** | Tier 1 + Scope + Permissions | Customer-facing agents, sensitive data access, multi-agent systems. Agents stay within defined boundaries with data flow control. |
| **Tier 3: Full Protocol** | All seven layers + containment | Autonomous agents, financial systems, regulated industries, critical infrastructure. Maximum protection with post-quantum crypto path. |

---

## Real Threats ASP Addresses

Agent security isn't hypothetical. Here are the attack patterns ASP is designed to catch.

### Prompt Injection Through Tool Outputs

An agent reads a web page, document, or email that contains hidden malicious instructions — telling it to ignore its operator's rules, exfiltrate data, or change its behavior. ASP's defense: the runtime distinguishes trusted operator instructions from untrusted external content. Untrusted content cannot modify scope, permissions, or logging.

### Data Exfiltration Through Allowed Tools

An agent with legitimate access to both a customer database and an email tool could combine those permissions to leak data. ASP's defense: data flow policies specify where data from each source may go, regardless of which tools the agent has access to.

### Privilege Escalation Through Delegation

A parent agent spawns child agents that collaborate to exceed what any single agent is authorized to do. ASP's defense: permission inheritance with ceilings, combined permission evaluation for lateral collaboration, and interaction boundaries for cross-domain access.

### The Slow Drift

An agent gradually expands its behavior over hundreds of actions, never crossing a bright line but ending up far from its declared purpose. ASP's defense: trajectory analysis in the coherence monitoring layer evaluates the cumulative direction of actions, not just individual steps.

---

## What ASP Is Honest About

A good security standard doesn't pretend to be perfect. ASP is transparent about its limitations.

### The Interpretability Gap

We cannot verify that an AI agent's stated reasoning reflects its actual computation. No one can — this is one of the biggest open problems in AI research. ASP doesn't claim to solve it. Instead, it builds a structure where the gap is detectable: if what an agent says doesn't match what it does, we catch it. As interpretability research advances, ASP gets stronger automatically because the architecture is designed to incorporate better tools.

### Sophisticated Adversaries

A highly capable agent that understands ASP could theoretically maintain perfect compliance while achieving unauthorized objectives. ASP makes this dramatically harder through defense-in-depth — the more layers an agent must satisfy simultaneously, the more constrained its options — but it does not claim to make it impossible. This is an honest boundary.

### Governance Is Hard

Who governs the identity infrastructure? Who verifies the verifiers? These are political and organizational challenges, not purely technical ones. ASP defines the technical properties the system must have and requires deployments to document their governance model. The field needs operational experience before a universal governance standard can be recommended.

---

## Customizing ASP for Your Needs

ASP is a floor, not a ceiling. It establishes the minimum requirements for safe agent deployment — and then gets out of your way.

### Start Where You Are

You don't need to implement all seven layers on day one. Start with Tier 1 (identity and audit logging). Add scope enforcement when your agents access sensitive systems. Move to full protocol when your agents operate autonomously. The tiers exist so you can grow into the standard.

### Extend for Your Domain

Financial services, healthcare, defense, and consumer applications all have different risk profiles and regulatory requirements. ASP's layers are the universal foundation. Your industry-specific requirements layer on top. The specification format (numbered requirements per layer) makes extension straightforward — add your requirements using the same structure.

### Test Your Implementation

The ASP specification includes conformance tests for each tier — specific pass/fail criteria that verify your implementation actually works, not just that the code exists. Beyond conformance testing, the spec includes red-teaming guidance: structured adversarial exercises that attempt to break your ASP implementation by forging credentials, injecting prompts through tool outputs, escalating privileges through delegation chains, and evading coherence monitoring.

---

## Call for Collaboration

ASP is an open specification, but open isn't enough. Agent security is a shared infrastructure problem — like internet trust, it only works if the ecosystem builds it together. ASP needs collaborators, critics, and implementers.

### Who We Want to Hear From

**AI labs and model providers.** Anthropic's work on AI safety — including Mythos (their cybersecurity initiative applying Claude to defensive security), the Frontier Safety Framework, and their broader commitment to safe deployment practices — represent exactly the kind of thinking ASP builds on. OpenAI, Google DeepMind, Meta AI, and others building frontier models have direct insight into what runtime safety controls are feasible, what internal reasoning can be externalized, and where the interpretability gap is narrowing. ASP should reflect that knowledge.

**Cybersecurity organizations and researchers.** MITRE, OWASP, CISA, the CNCF security community, and independent security researchers understand threat modeling, conformance testing, and what it takes to make a standard credible to practitioners. ASP's threat model and conformance tests need adversarial review from people who break systems for a living.

**Agent platform providers.** Companies building agent deployment infrastructure — cloud providers, orchestration frameworks, tool-use platforms — are the ones who would implement ASP at the runtime level. Their engineering feedback determines whether ASP is practical or purely theoretical.

**Regulatory and governance bodies.** The EU AI Act, NIST AI RMF, ISO 42001, and emerging agent-specific regulatory thinking all inform what compliance actually looks like. ASP should complement these frameworks, not duplicate or conflict with them.

**Practitioners who deploy agents today.** L&D professionals, automation engineers, operations teams, and developers who build with tools like n8n, LangChain, CrewAI, and Claude — the people who see agent failure modes in production, not in theory. ASP was born from this perspective, and it should continue to be shaped by it.

### How to Contribute

The ASP specification and this companion guide are published on GitHub under CC BY-SA 4.0. You can file issues, propose changes, submit implementation reports, share red-team findings, or fork and extend for your domain. The specification improves with every contribution.

If you're working on agent safety, agent governance, or AI security standards in any capacity — we want to align. Reach out, open an issue, or start building. The window for establishing these norms is narrow, and the work is better done together.

---

## Why This Exists

ASP was not designed in a research lab. It was developed through iterative conversation — identifying real problems with how agents are deployed today and designing practical solutions for each one.

The author is a Learning & Development professional with deep experience in AI automation — someone who works with these tools daily and saw the security gap clearly. Good ideas don't require a computer science degree. They require proximity to the problem and the discipline to think it through.

The window for establishing agent security norms is narrow. We are in a moment where the tools are powerful enough to be dangerous and the standards haven't caught up. ASP exists to close that gap before a high-profile incident forces a reactive, fragmented response.

If you deploy agents, ASP is for you. If you build agent platforms, ASP is for you. If you regulate technology, ASP is for you. If you just want to understand how AI agents can be made safe, ASP is for you.

---

## Next Steps

Read the full technical specification ([ASP.md](ASP.md)) for the complete layer-by-layer requirements, reference schemas, threat models, conformance tests, and implementation guidance.

Start with Tier 1. Implement identity and audit logging for your agent deployments. See what it reveals. Then decide if you need more.

Red-team your implementation. Don't just verify that the code paths exist — try to break them.

Contribute. File issues, propose extensions, share implementation experience. The specification lives on GitHub and improves with every contribution.

---

*ASP is an open standard. Agent security is a shared problem. This is the beginning of the solution.*
