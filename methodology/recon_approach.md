# Reconnaissance Approach

## Methodology

This assessment follows a **black-box methodology** — the researcher interacts with Hermes Agent as an end user would, without source-level access to security controls or internal documentation beyond what is publicly available.

### Information Sources

| Source | Type | Purpose |
|--------|------|---------|
| Hermes Agent GitHub repo | Public | Architecture, security docs, issues, CVEs |
| Official documentation | Public | Security model, configuration, deployment guides |
| hermes CLI output | Black-box | Runtime behavior, error messages, default configs |
| Filesystem enumeration | Black-box | Memory files, session databases, config structure |
| Published research | Public | Repello AI threat model, CVE reports, academic papers |
| NemoClaw research | Internal | Prior assessment methodology (adapted for Hermes) |

### Approach per Phase

**Phase 1 — Reconnaissance**
- Install Hermes Agent from scratch on a clean VM
- Enumerate default file structure: ~/.hermes/ contents, memory files, session databases
- Map the 7 security layers by testing each boundary independently
- Document default configuration: approvals.mode, redaction settings, backend selection
- Identify credentials and sensitive files accessible from within the agent runtime

**Phase 2 — Gateway Testing**
- Configure Telegram + Discord bots on test accounts
- Test authorization controls from unauthorized accounts
- Probe input handling with crafted payloads
- Assess dangerous command approval and hardline blocklist

**Phase 3 — Memory Testing**
- Craft injection payloads in three categories: classic patterns, encoded patterns, social engineering
- Test memory scanner detection rates for each category
- Verify cross-session persistence with agent restarts
- Test indirect memory poisoning via tool results

**Phase 4 — Context File Testing**
- Create test repositories with malicious context files
- Test scanner evasion with Brainworm-style payloads
- Attempt C2 establishment if scanner is evaded

**Phase 5 — Cross-Platform Testing**
- Multi-platform gateway configuration
- Cross-platform memory contamination testing
- Trust boundary violation documentation

## Severity Ratings

Findings use the same severity scale as the NemoClaw assessment:

| Severity | Criteria |
|----------|----------|
| CRITICAL | Remote code execution, full credential compromise, sandbox escape |
| HIGH | Credential disclosure, persistent memory poisoning, data exfiltration preparation |
| MEDIUM | Information disclosure, scanner evasion, partial bypass of security controls |
| LOW | Minor information leak, configuration weakness, defense-in-depth gap |
| INFORMATIONAL | Architectural observation, positive finding, documentation note |

## Framework Mapping

Every finding will include:

- **OWASP Agentic Security Top 10** category
- **MITRE ATLAS** tactic/technique (where applicable)
- **CWE** identifier
- **Comparison to NemoClaw findings** (where relevant)

---

*Adapted from the NemoClaw Security Research methodology (March 2026)*
