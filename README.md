# Aurora AI Security — Masterschool Cybersecurity Capstone

**Author:** Bernd Janzen  
**Programme:** Masterschool Cybersecurity Bootcamp  
**Timeline:** February – May 2026  
**Status:** 🔄 Segment 3 In Progress

---

## Project Overview

Complete enterprise network design and security infrastructure implementation for **Aurora AI Security GmbH** — a fictional AI security consultancy operating from Freiburg (HQ) and Basel (Branch). The project demonstrates end-to-end cybersecurity engineering across 4 segments.

**Core competencies demonstrated:**
- Network architecture & VLAN segmentation (pfSense, Cisco)
- SIEM deployment & security monitoring (Wazuh 4.14.3)
- Firewall configuration & inter-VLAN routing
- STRIDE threat modeling & risk assessment
- Compliance framework implementation (GDPR, NIS2, IT-SiG 2.0, FADP)
- Device hardening (pfSense, Alpine Linux, Ubuntu, Windows 11, Cisco, FortiGate)
- SOC workflow design & incident response
- Penetration testing & security validation (Segment 4)

---

## Project Segments

### ✅ Segment 1: Network Planning (COMPLETE)
- Full enterprise network design — Freiburg HQ + Basel Branch
- 13 VLANs (VLAN 10–999), hierarchical IP scheme (10.10.x.x / 10.11.x.x)
- NIST CSF 2.0 framework selection with regulatory mapping (GDPR, NIS2, IT-SiG, FADP)
- BSI P-A-P firewall architecture, Zero Trust design principles
- **Deliverable:** Segment1_NetworkPlan_Janzen.pdf ✅

### ✅ Segment 2: Virtual Infrastructure Build (COMPLETE — PDF pending)
- 8 VMs deployed in UTM on MacBook Air M3 (Apple Silicon)
- Proper VLAN segmentation via separate UTM bridges (VLAN30, VLAN40, WAN)
- pfSense: WAN + VLAN30 (172.16.30.1) + VLAN40 (172.16.40.1) — inter-VLAN routing confirmed
- Wazuh 4.14.3 SIEM operational at 172.16.30.10 — agent active on Win11
- pfSense syslog → Wazuh UDP 514 — confirmed via tcpdump (extension credit)
- Alpine switches reconfigured to correct VLAN IPs (172.16.30.11 / 172.16.40.11)
- **Deliverable:** Segment2_VirtualBuild_Janzen.html (ready to print to PDF)

### 🔄 Segment 3: Security & SOC Planning (IN PROGRESS — started April 14)
- ✅ STRIDE threat model — 5 threats scored across full production network
- ✅ NIST CSF 2.0 framework justification (vs ISO 27001 & MITRE ATT&CK, ~350 words)
- ✅ Device hardening plan — 6 device types (pfSense, Alpine, Wazuh, Win11, Cisco, FortiGate)
- ✅ Logging & monitoring plan — log sources, retention policy, alerting rules, dashboards
- 🔜 Firewall rules & ACL design per VLAN zone
- 🔜 SOC roles & workflow definition
- 🔜 Incident response playbook
- 🔜 Implementation roadmap
- **Deliverable:** Segment3_SecuritySOCPlan_Janzen.pdf

### 📅 Segment 4: Implementation & Validation (starts ~April 28)
- Deploy security controls in pfSense (zone-policy firewall rules)
- Configure Wazuh alerts & dashboards
- Penetration testing from Kali Linux (external attack simulation)
- SOC workflow simulation & incident response validation
- Extensions planned: Suricata IDS, HashiCorp Vault, Python automation
- **Deliverable:** Final validation PDF (capstone)

---

## Lab Infrastructure

| Component | Details |
|-----------|---------|
| Host | MacBook Air M3, 16GB RAM |
| Hypervisor | UTM 4.x (QEMU / Apple Virtualization) |
| Router/Firewall | pfSense 2.7.2 AMD64 |
| SIEM | Wazuh 4.14.3 (Manager + Indexer + Dashboard + Filebeat) |
| Switches | Alpine Linux 3.23.3 ARM64 (x2) |
| Target Endpoint | Windows 11 Pro ARM64 |
| Attacker | Kali Linux 2024 ARM64 |
| VLAN30 | 172.16.30.0/24 — IT & Infrastructure |
| VLAN40 | 172.16.40.0/24 — Consulting |
| WAN | 192.168.64.0/24 — External / Kali |

---

## Technology Stack

**Virtualization:** UTM (QEMU) on Apple Silicon M3  
**Router/Firewall:** pfSense Community Edition 2.7.2  
**SIEM:** Wazuh 4.14.3 (open-source Splunk/Sentinel equivalent)  
**Switches:** Alpine Linux 3.23.3 (virtual L2 switches)  
**Endpoints:** Windows 11 ARM, Kali Linux 2024, Ubuntu 22.04  
**Framework:** NIST CSF 2.0  
**Compliance:** GDPR, NIS2, IT-SiG 2.0, FADP, BSI IT-Grundschutz  

---

## Skills Demonstrated

- Network Engineering (VLANs, inter-VLAN routing, TCP/IP, pfSense)
- Security Operations (Wazuh SIEM, syslog, log analysis, alerting)
- Threat Modeling (STRIDE methodology, risk scoring)
- Compliance & GRC (GDPR, NIS2, IT-SiG 2.0, NIST CSF 2.0)
- Device Hardening (Linux, Windows, pfSense, Cisco IOS, FortiOS)
- System Administration (Linux CLI, Windows, Alpine, Ubuntu)
- Security Testing (Kali Linux, penetration testing — Segment 4)

---

## Career Objective

Seeking entry-level cybersecurity position in Germany (remote-friendly):
- SOC Analyst / Security Operations
- Network Security Engineer
- GRC / Compliance Analyst

**In progress:** CompTIA Network+ | Eramba GRC Training (May 2026)

---

*Repository: https://github.com/manjou/aurora-ai-security*  
*Updated: April 2026*
