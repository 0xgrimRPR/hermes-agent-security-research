# Hermes Agent Security Research

**Black-box security assessment of Hermes Agent — Nous Research's open-source autonomous AI agent framework**

> Second project in the AI Agent Security Assessment series.  
> First project: [NemoClaw Security Research](https://github.com/0xgrimRPR/nemoclaw-security-research) (NVIDIA NemoClaw, March–May 2026)

---

## Why This Matters

Hermes Agent is the fastest-growing open-source AI agent framework of 2026 — 140,000+ GitHub stars in under three months, processing over 224 billion daily tokens on OpenRouter. Unlike coding assistants tied to an IDE, Hermes is a **persistent, self-improving autonomous agent** that runs on your own infrastructure, connects to 22+ messaging platforms, executes terminal commands, browses the web, and accumulates knowledge across sessions through persistent memory.

The same memory that makes the product work is also its largest attack surface. No hands-on red team assessment has been published against Hermes Agent to date — existing research is limited to theoretical threat models and two disclosed CVEs (path traversal in platform adapters). This project fills that gap with practical, proof-of-concept-driven security research.

---

## Research Status

| Phase | Focus | Status |
|-------|-------|--------|
| Phase 1 | Reconnaissance & Architecture Mapping | 🔜 Planned |
| Phase 2 | Gateway & Messaging Attack Surface | 🔜 Planned |
| Phase 3 | Memory Poisoning & Cross-Session Persistence | 🔜 Planned |
| Phase 4 | Context File Injection & Promptware C2 | 🔜 Planned |
| Phase 5 | Cross-Platform Attack Chain ⚠️ **Novel Research** | 🔜 Planned |

---

## Target Overview

| Component | Details |
|-----------|---------|
| Target | Hermes Agent (Nous Research) |
| License | MIT |
| Release | February 25, 2026 |
| GitHub | github.com/NousResearch/hermes-agent |
| Language | Python (pipx install hermes-agent) |
| Memory | SQLite + FTS5, persistent MEMORY.md / USER.md |
| Execution Backends | Local, Docker, SSH, Singularity, Modal, E2B |
| Messaging Platforms | 22+ (Telegram, Discord, Slack, WhatsApp, Signal, Matrix, Teams, etc.) |
| Skill System | Community marketplace + self-authored skills |
| MCP Support | Full MCP client with stdio subprocess isolation |
| Known CVEs | CVE-2026-7396 (path traversal, WeChat adapter), CVE-2026-6829 (WebUI path traversal) |

---

## Prior Research & Gaps

### What exists

- **Repello AI** — Enterprise threat model (theoretical, no PoCs)
- **CVE-2026-7396 / CVE-2026-6829** — Path traversal vulnerabilities (patched)
- **Issue #496** — Nous Research's own Promptware Defense tracking (open, unresolved)
- **Issue #6320** — Session/memory contamination between profiles (documented)
- **Issue #17691** — HERMES_REDACT_SECRETS off by default, credential leak via gateway (documented)
- **"Sleeper Channels" paper** — Academic analysis of persistent prompt injection (arxiv, May 2026)

### What's missing

| Gap | Description |
|-----|-------------|
| End-to-end attack chain | No practical testing of message injection → memory poisoning → persistence → exfiltration |
| Context file scanner evasion | Nous Research acknowledges in #496 that Brainworm payloads bypass regex detection |
| Cross-platform contamination | #6320 covers multi-profile leak; nobody has tested cross-platform within one profile |
| Malicious skill PoC | Skill marketplace risk flagged theoretically; no practical attack demonstrated |
| Memory persistence exploitation | Persistent memory is the defining feature AND largest attack surface — untested |
| Default config security posture | Local backend (no sandbox), redaction off — nobody assessed out-of-box security |

---

## Phase Details

### Phase 1 — Reconnaissance & Architecture Mapping

Map the seven documented security layers from a black-box perspective. Install from scratch, document the memory filesystem, enumerate context files auto-loaded into the system prompt, map execution backends and their isolation postures, and audit the default security configuration.

### Phase 2 — Gateway & Messaging Attack Surface

Assess the messaging gateway daemon — the primary external-facing attack surface. Test the pairing-code authorization system, credential redaction defaults, input sanitization, dangerous command approval bypass, and the hardline blocklist.

**Key hypothesis:** A compromised messaging account with gateway access is functionally equivalent to shell access on the host under the default local backend.

### Phase 3 — Memory Poisoning & Cross-Session Persistence

Test the persistent memory layer for injection, poisoning, and cross-session persistence. Craft payloads that evade the regex-based memory scanner using social engineering instead of classic injection phrases. Verify whether poisoned memory persists across sessions and agent restarts.

**Relevance to NemoClaw:** NemoClaw Phase 4 demonstrated session-level memory contamination (F-27). Hermes's persistent memory means the equivalent attack survives indefinitely across sessions.

### Phase 4 — Context File Injection & Promptware C2

Test the complete Brainworm attack chain: plant malicious AGENTS.md / .cursorrules in a repository, evade the context file scanner, and establish a C2 loop using the agent's own tools.

**Key hypothesis:** Nous Research acknowledges in Issue #496 that current scanning would not catch social-engineering-based promptware.

### Phase 5 — Cross-Platform Attack Chain ⚠️

**Novel research — no prior published work covers this vector.**

Demonstrate that Hermes's single-gateway, multi-platform architecture enables cross-platform attack chains. Poison memory via a low-trust platform (e.g., public Discord server) and verify contamination propagation to a high-trust platform (e.g., owner's private Telegram). The agent's memory is shared across all connected platforms within one profile — this creates an unexamined trust boundary violation.

---

## Framework Alignment

All findings mapped against:

- **OWASP Agentic Security Top 10** — Primary mapping
- **MITRE ATLAS** — AI-specific tactics and techniques
- **NIST SP 800-63-4** — Authentication/authorization assessment
- **NIST AI RMF** — Risk management governance
- **CWE** — Technical vulnerability classification

---

## NemoClaw vs. Hermes — Why a New Assessment

| Dimension | NemoClaw (Completed) | Hermes Agent (This Project) |
|-----------|---------------------|----------------------------|
| Type | Sandbox-isolated coding agent | Persistent autonomous agent |
| Memory | Session-only (no persistence) | Persistent SQLite + MEMORY.md + USER.md |
| Isolation | Mandatory sandbox (Landlock, seccomp) | Optional (local backend has none) |
| External Access | API endpoint only | 22+ messaging platforms + web + MCP |
| Skill System | Fixed toolset | Self-authoring skills + marketplace |
| Attack Surface | Sandbox escape + prompt injection | Gateway + memory + skills + MCP + context files |
| Session Lifetime | Minutes to hours | Days to months (always-on daemon) |

---

## Repository Structure

```
hermes-agent-security-research/
├── README.md                              ← You are here
├── DISCLAIMER.md
├── reports/
│   ├── Hermes_Security_Report_Phase1.md
│   ├── Hermes_Security_Report_Phase2.md
│   ├── Hermes_Security_Report_Phase3.md
│   ├── Hermes_Security_Report_Phase4.md
│   ├── Hermes_Security_Report_Phase5.md
│   └── findings/
│       └── F-01_*.md through F-XX_*.md
└── methodology/
    ├── recon_approach.md
    └── attack_methodology.md
```

---

## About This Research

This project is part of a personal portfolio focused on AI security and Red Team research. The assessment is conducted in a self-hosted environment using publicly available software. No production systems are targeted. All testing is performed against a local installation under controlled conditions.

**Researcher:** Mike  
**Handle:** 0xgrimRPR  
**Role:** Junior Cybersecurity Engineer | Red Team Track  
**Research focus:** AI agent security, autonomous system threat modeling, prompt injection  
**Previous work:** [NemoClaw Security Research](https://github.com/0xgrimRPR/nemoclaw-security-research) — 4-phase black-box assessment of NVIDIA NemoClaw

---

## Disclaimer

This research is conducted for educational and professional development purposes. See [`DISCLAIMER.md`](DISCLAIMER.md) for full responsible disclosure policy and scope statement.

*Hermes Agent is created by Nous Research and distributed under the MIT License. This project is not affiliated with or endorsed by Nous Research.*
