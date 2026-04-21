# Week Plan — Segment 3 Completion
**Target deadline:** ~April 28, 2026

---

## Priority 1 — Official Segment 3 Requirements

| Day | Section | Effort | Notes |
|-----|---------|--------|-------|
| Mon/Tue | Section 3 — Firewall Rules & ACL Design | ~2h | Most technical section — builds directly on pfSense rules from Segment 2. Interview with mentor style. |
| Tue/Wed | Section 6 — SOC Roles & Workflow | ~1.5h | Lighter — define 3 SOC functions, map to Aurora org structure |
| Wed/Thu | Section 7 — Incident Response Playbook | ~1.5h | One playbook per threat type (5 threats from Section 1) |
| Thu | Section 8 — Implementation Roadmap | ~1h | Gantt-style table sequencing all controls |
| Fri | Build HTML + compile PDF | ~1.5h | Same approach as Segment 2 — HTML → print to PDF |

---

## Priority 2 — Extensions (if time allows, good for portfolio)

### Option A: GRC Tool — OpenRMF or Eramba
- **What:** Open-source GRC platforms for managing controls, risks, and compliance
- **Why relevant:** Forward Earth needs SOC 2 + ISO 27001 — GRC tools are how companies manage those certifications
- **SOC 2 vs ISO 27001:** SOC 2 is a US audit standard (Trust Service Criteria), ISO 27001 is international certifiable standard — both map well to NIST CSF
- **Action:** Install Eramba Community Edition as a VM, map Aurora's NIST CSF controls into it
- **Portfolio value:** Shows hands-on GRC tool experience — rare for bootcamp students

### Option B: Network Monitoring — Zabbix
- **What:** Open-source network monitoring (uptime, performance, SNMP)
- **Why relevant:** Segment 3 extension idea from the guidelines — "Install Zabbix agent on endpoints"
- **Action:** Deploy Zabbix as a VM in VLAN 30, add agents to Win11 and Alpine switches
- **Portfolio value:** Shows NMS experience alongside SIEM

### Option C: Suricata IDS (strongest extension)
- **What:** Open-source IDS/IPS — deploy inline on pfSense
- **Why relevant:** Directly mentioned in Segment 3 extension ideas
- **Action:** Install Suricata package on pfSense, configure rules for VLAN monitoring
- **Portfolio value:** IDS/IPS is a core SOC tool — very strong for interviews

---

## Forward Earth / GRC Internship Context

Forward Earth requires **SOC 2** and **ISO 27001**. Key points to know:

| | SOC 2 | ISO 27001 |
|---|-------|-----------|
| Origin | US (AICPA) | International (ISO) |
| Type | Audit report | Certifiable standard |
| Focus | Trust Service Criteria (Security, Availability, Confidentiality) | Information Security Management System (ISMS) |
| Relevant for | SaaS companies, US clients | EU clients, regulatory requirements |
| Maps to NIST CSF | Yes | Yes |

Aurora's NIST CSF 2.0 foundation maps to both — mention this in any GRC interview.

---

## Recommendation

Focus on **Priority 1 first** — finish Segment 3 by Thursday. Then if energy allows, add **Suricata** as the extension (directly in the guidelines, strongest technical signal for SOC roles) or **Eramba** (strongest signal for GRC roles like Forward Earth).
