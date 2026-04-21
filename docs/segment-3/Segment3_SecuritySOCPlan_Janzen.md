# Segment 3: Security & SOC Plan
## Aurora AI Security GmbH

**Author:** Bernd Janzen  
**Programme:** Masterschool Cybersecurity Bootcamp  
**Started:** April 14, 2026  
**Framework:** NIST CSF 2.0  
**Scope:** Full production network — Freiburg HQ + Basel Branch (Segment 1 design)

---

## Document Outline

1. Threat Model & Risk Matrix (STRIDE)
2. Security Framework Justification (NIST CSF 2.0)
3. Firewall Rules & ACL Design
4. Device Hardening Plan
5. Logging & Monitoring Plan
6. SOC Roles & Workflow Definition
7. Incident Response Playbook
8. Implementation Roadmap

---

## 1. Threat Model & Risk Matrix

### Methodology: STRIDE

STRIDE is a threat modeling framework developed by Microsoft. It categorizes threats into six types to systematically identify what can go wrong across a network:

| Letter | Threat Type | Description |
|--------|-------------|-------------|
| **S** | Spoofing | Attacker impersonates a legitimate user, device, or service |
| **T** | Tampering | Attacker modifies data, code, or configurations without authorization |
| **R** | Repudiation | Attacker denies performing an action due to lack of audit trail |
| **I** | Information Disclosure | Attacker reads data they should not have access to |
| **D** | Denial of Service | Attacker makes a system or service unavailable |
| **E** | Elevation of Privilege | Attacker gains more access rights than authorized |

### Scope

Threats are assessed against the full Aurora production network:
- **Freiburg HQ:** VLANs 10–999 (10.10.x.x)
- **Basel Branch:** VLANs 30–80 (10.11.x.x)
- **Inter-site:** IPsec VPN tunnel (FortiGate NGFW)
- **Internet-facing:** DMZ VLAN 100, client web portal

### Risk Scoring

**Risk Score = Likelihood × Impact** (each rated 1–5)

| Score | Risk Level |
|-------|------------|
| 1–4   | Low |
| 5–9   | Medium |
| 10–14 | High |
| 15–25 | Critical |

---

### Risk Matrix

| # | Threat | STRIDE | Target | Likelihood (1–5) | Impact (1–5) | Risk Score | Risk Level |
|---|--------|--------|--------|-----------------|--------------|------------|------------|
| 1 | Social Engineering / Phishing / Whaling | I, E | VLAN 40 (Consulting), VLAN 20 (HR) | 3 | 4 | **12** | 🔴 High |
| 2 | Dirty Lab Sandbox Escape / Unauthorized Access | I, T, E | VLAN 51 (Dirty Lab) | 2 | 4 | **8** | 🟠 Medium-High |
| 3 | DDoS Attack on IPsec VPN Tunnel | D | Basel–Freiburg VPN (FortiGate 40F) | 3 | 4 | **12** | 🔴 High |
| 4 | Client Data Theft via DMZ Web Portal | S, I, E | VLAN 100 (DMZ) | 2 | 4 | **8** | 🟠 Medium-High |
| 5 | Log Deletion After Privilege Escalation | R | VLAN 30 (SIEM/IT Infrastructure) | 2 | 4 | **8** | 🟠 Medium-High |

---

### Threat Descriptions

#### Threat 1 — Social Engineering / Phishing / Whaling
**Description:** An attacker sends targeted phishing emails to Aurora staff — or highly personalized "whaling" attacks against executives (CEO Alex Brandt, CFO Jordan) — to steal credentials. Given Aurora's high-value client data (AI audit reports, compliance assessments, vulnerability findings), the firm is an attractive target.

**Attack Path:**
1. Consultant on VLAN 40 receives convincing phishing email
2. Credentials stolen → attacker gains access to Consulting zone
3. Attacker accesses confidential client audit reports and engagement data
4. If victim has cross-VLAN access (e.g., shared file server), lateral movement to VLAN 20 (HR) or VLAN 50 (AI Lab) is possible

**Business Impact:** Client confidentiality breach, GDPR violation (employee/client PII), reputational damage — severe for a trust-based consultancy.

**STRIDE:** Information Disclosure (client data read), Elevation of Privilege (pivot to other VLANs via victim's access rights)

**Mitigations:**
- MFA on all accounts (already mandated by NIS2 requirement in Segment 1)
- Security awareness training
- Email filtering / anti-phishing gateway
- Least-privilege access — consultants should only access their own engagement data
- SIEM alerts on anomalous login times/locations

---

#### Threat 2 — Dirty Lab Sandbox Escape / Unauthorized Access to VLAN 51
**Description:** VLAN 51 (Dirty Lab Sandbox) hosts active exploit tools, adversarial AI models, and client AI systems under test. An attacker who gains access — or an exploit tool that breaks containment — could access proprietary client AI models or tamper with red team test results.

**Attack Path:**
1. Attacker compromises a researcher's workstation in VLAN 51
2. Access to client AI training datasets and adversarial tools
3. Tampers with audit results — Aurora delivers a falsely "clean" report to a client
4. Or: exploit tool escapes VLAN 51 isolation → lateral movement to VLAN 40/30

**Business Impact:** Tampered audit results = professional fraud. Client AI model theft. If exploit tools escape sandbox → potential full network compromise. Aurora's legal liability is extreme.

**STRIDE:** Information Disclosure (client AI model theft), Tampering (falsified audit results), Elevation of Privilege (sandbox escape to production VLANs)

**Mitigations:**
- Strict egress filtering on VLAN 51 (no outbound except explicitly allowed)
- No persistent storage of client models beyond engagement duration
- Separate credential set for Dirty Lab access (not shared with production)
- Wazuh monitoring of all VLAN 51 activity

---

#### Threat 3 — DDoS Attack on IPsec VPN Tunnel
**Description:** A volumetric DDoS attack targets the FortiGate 40F HA pair in Basel, saturating the IPsec tunnel between Freiburg and Basel. All cross-border traffic — client data, audit tools, shared resources — becomes unavailable.

**Attack Path:**
1. Attacker (potentially a disgruntled threat actor exposed by Aurora) launches volumetric DDoS
2. FortiGate 40F in Basel overwhelmed → IPsec tunnel drops
3. Basel consultants lose access to Freiburg HQ resources mid-engagement
4. Business operations disrupted, SLAs breached

**Business Impact:** Revenue loss (billing disruption), SLA breach, client delivery failure. AI-backed DDoS attacks are increasingly large and sophisticated, making this threat more realistic over time.

**STRIDE:** Denial of Service

**Mitigations:**
- FortiGate HA pair (already in design) provides redundancy
- ISP-level DDoS scrubbing service
- Rate limiting on VPN gateway
- Backup connectivity (LTE failover for Basel branch)
- Incident response runbook for VPN outage

---

#### Threat 4 — Client Data Theft via DMZ Web Portal
**Description:** Aurora's client-facing web portal (VLAN 100 DMZ) allows clients to access audit reports, upload AI models for testing, and communicate with the team. An attacker exploits a vulnerability (unpatched CMS, broken authentication, zero-day) or uses stolen client credentials to access confidential audit reports.

**Attack Path:**
1. Attacker discovers vulnerability in web portal or steals client credentials
2. Authenticates as legitimate client (Spoofing)
3. Downloads confidential audit reports containing competitor vulnerability data
4. Reports contain detailed weaknesses of client infrastructure — attacker has a roadmap to attack Aurora's clients

**Business Impact:** Client confidentiality breach, legal liability, GDPR violation if EU client PII involved. Aurora becomes the supply chain attack vector against its own clients.

**STRIDE:** Spoofing (impersonating legitimate client), Information Disclosure (reading confidential reports), Elevation of Privilege (accessing other clients' data)

**Mitigations:**
- Web Application Firewall (WAF) in front of DMZ portal
- Regular vulnerability scanning and patching of web portal
- Client MFA on portal login
- Strict data isolation — each client can only access their own reports
- Penetration testing of the portal (Aurora should test their own systems)

---

#### Threat 5 — Log Deletion After Privilege Escalation
**Description:** An attacker who has already gained admin-level access (via compromised credentials or privilege escalation) deletes or tampers with SIEM logs in VLAN 30 to cover their tracks. This prevents forensic investigation and violates NIS2's 6-month log retention requirement.

**Attack Path:**
1. Attacker compromises admin account (via phishing, credential stuffing, or insider threat)
2. Performs malicious action (data exfiltration, configuration change)
3. Deletes or corrupts SIEM logs in Wazuh/VLAN 30 to eliminate evidence
4. Incident cannot be investigated — Aurora cannot determine scope, timeline, or impact

**Business Impact:** NIS2 compliance violation (mandatory 6-month retention), inability to investigate own incident, loss of forensic evidence. For a security consultancy — catastrophic reputational damage if they cannot investigate their own breach.

**STRIDE:** Repudiation (no audit trail, attacker denies actions)

**Mitigations:**
- Write-once / append-only log storage (logs cannot be deleted, only appended)
- SIEM in VLAN 30 isolated — admin access requires separate privileged credentials
- Real-time log forwarding to immutable off-site backup (cloud or tape)
- Alert on any attempt to stop/modify SIEM services
- Regular integrity checks on log storage

---

---

## 2. Security Framework Justification — NIST CSF 2.0

As outlined in our Segment 1 network design, Aurora AI Security GmbH selected NIST CSF 2.0 over ISO 27001 and MITRE ATT&CK as our foundational security framework. As a startup-stage consultancy, CSF provides the right balance of structure and flexibility for our current maturity level, with ISO 27001 certification as a 3–5 year goal.

NIST CSF 2.0 provides Aurora with a flexible framework for internal risk management. As a startup, the cost and operational overhead of ISO 27001 certification is not yet justified. CSF gives us the structural guidance to build a mature security program first, with ISO 27001 certification as a 3–5 year goal once the organisation has the budget and process maturity. Importantly, CSF is framework-agnostic and maps directly to ISO 27001 controls — implementing CSF now does not block certification later.

MITRE ATT&CK focuses exclusively on adversary tactics and techniques at a technical level. It is valuable for threat detection and SOC operations, but does not address governance, compliance obligations, or business risk management. NIST CSF covers the full lifecycle — from governance and risk identification through to response and recovery. MITRE ATT&CK is used as a complementary tool within the Detect and Respond functions of CSF.

As a German consultancy with cross-border operations, Aurora operates under GDPR, NIS2, IT-SiG 2.0, and Swiss FADP. NIST CSF's risk-based structure maps efficiently across all four regulations without duplication. For GDPR, the Identify function satisfies data inventory requirements (Art. 30) and DPIAs (Art. 35), while Protect implements technical measures (Art. 32 encryption, access controls) and Respond ensures our 72-hour breach notification capability (Art. 33). Under NIS2, Detect and Respond address incident handling timelines (24h/72h reporting under Art. 23), while Identify covers supply chain risk management (Art. 21.2d). For IT-SiG 2.0, our Wazuh SIEM deployment directly implements the required detection systems (§8a BSIG), and device hardening ensures "state of the art" security. Cross-border operations with Basel fall under Swiss FADP, addressed through the Protect function's AES-256 IPsec encryption requirement. NIST CSF acts as the backbone framework, allowing Aurora to demonstrate compliance across multiple regulatory regimes with a single, coherent security program.

---

---

## 3. Firewall Rules & ACL Design

### Design Principles

All firewall rules follow the **default-deny** policy — all traffic is blocked unless explicitly permitted. This implements the **least privilege principle**: every device and user can only access what they specifically need to do their job, nothing more.

Rules are applied **per interface** in pfSense/FortiGate — inbound traffic on each interface is evaluated against that VLAN's rule set before being forwarded.

**Rule evaluation order:** Top to bottom — first match wins. Specific rules always above general deny rules.

---

### 3.1 Zone Overview & Trust Levels

| VLAN | Name | Trust Level | Rationale |
|------|------|-------------|-----------|
| 10 | Management | Highest | IT admins only — controls all infrastructure |
| 20 | HR & Finance | High | GDPR-protected PII — strictly isolated |
| 30 | IT & Infrastructure | High | SIEM and core IT — must receive from all zones |
| 40 | Consulting | Medium | Client-facing work — internet access needed |
| 50 | AI Research Lab | Medium-High | Sensitive client AI models — limited external access |
| 51 | Dirty Lab Sandbox | Maximum Restriction | Active exploits — near-total isolation |
| 60 | Printers | Low | Receives print jobs only — no outbound |
| 70 | IoT Devices | Lowest | Untrusted devices — fully isolated |
| 80 | VoIP | Low-Medium | Voice traffic only — no data access |
| 100 | DMZ | External-facing | Internet-reachable — strict inbound filtering |
| 999 | Guest WiFi | Untrusted | Internet only — no internal access |

---

### 3.2 Firewall Rules per VLAN

#### VLAN 10 — Management
*IT admins and Jump Server. Highest trust — can reach all VLANs for administration.*

| # | Action | Protocol | Source | Destination | Port | Purpose |
|---|--------|----------|--------|-------------|------|---------|
| 1 | PASS | TCP | VLAN10 net | Any VLAN | 22 | SSH admin access to all devices |
| 2 | PASS | TCP | VLAN10 net | Any VLAN | 443/80 | Web UI access (pfSense, Wazuh dashboard) |
| 3 | PASS | TCP | VLAN10 net | Any VLAN | 3389 | RDP for Windows admin |
| 4 | PASS | Any | VLAN10 net | VLAN30 | * | Full access to IT Infrastructure zone |
| 5 | DENY | Any | Any | VLAN10 | * | Block all inbound to Management from other VLANs |

---

#### VLAN 20 — HR & Finance
*HR staff and SAP ERP server. GDPR-protected — maximum data isolation.*

| # | Action | Protocol | Source | Destination | Port | Purpose |
|---|--------|----------|--------|-------------|------|---------|
| 1 | PASS | TCP | VLAN20 net | VLAN20 net | * | Internal HR zone communication |
| 2 | PASS | TCP | VLAN20 net | VLAN30 (Wazuh) | 1514/1515 | Wazuh agent → SIEM |
| 3 | PASS | TCP | VLAN20 net | VLAN60 | 9100 | Printing |
| 4 | PASS | TCP | VLAN20 net | VLAN100 (DMZ) | 443 | HTTPS internet access |
| 5 | PASS | TCP | VLAN10 (Jump Server) | VLAN20 | 22/443 | IT admin access via Jump Server only |
| 6 | DENY | Any | Any | VLAN20 | * | Block all other inbound — no direct access from other VLANs |
| 7 | DENY | Any | VLAN20 | VLAN40/50/51 | * | HR cannot reach Consulting, AI Lab, or Dirty Lab |

---

#### VLAN 30 — IT & Infrastructure
*SIEM, core servers, NTP, Jump Server. Receives logs from all zones.*

| # | Action | Protocol | Source | Destination | Port | Purpose |
|---|--------|----------|--------|-------------|------|---------|
| 1 | PASS | TCP/UDP | Any VLAN | VLAN30 (Wazuh) | 1514/1515 | Wazuh agent connections from all zones |
| 2 | PASS | UDP | Any VLAN | VLAN30 (Wazuh) | 514 | Syslog from network devices (pfSense, switches) |
| 3 | PASS | UDP | Any VLAN | VLAN30 (NTP) | 123 | NTP time sync from all devices |
| 4 | PASS | TCP | VLAN30 | Any VLAN | 22 | SIEM/admin outbound SSH for management |
| 5 | PASS | TCP | VLAN10 | VLAN30 | 443 | Wazuh dashboard access from Management only |
| 6 | DENY | TCP | VLAN40/50/51 | VLAN30 | 443 | Block dashboard access from non-management VLANs |
| 7 | DENY | Any | Any | VLAN30 | * | Default deny all other inbound |

---

#### VLAN 40 — Consulting
*Consulting workstations. Internet access needed. No access to sensitive zones.*

| # | Action | Protocol | Source | Destination | Port | Purpose |
|---|--------|----------|--------|-------------|------|---------|
| 1 | PASS | TCP | VLAN40 net | VLAN30 (Wazuh) | 1514/1515 | Wazuh agent → SIEM |
| 2 | PASS | TCP | VLAN40 net | VLAN60 | 9100 | Printing |
| 3 | PASS | UDP | VLAN40 net | VLAN80 | 5060/5061 | VoIP calls (SIP protocol) |
| 4 | PASS | TCP | VLAN40 net | VLAN100 (DMZ) | 443/80 | Internet access via DMZ gateway |
| 5 | PASS | TCP | VLAN10 (Jump Server) | VLAN40 | 22/3389 | IT admin access via Jump Server only |
| 6 | DENY | Any | VLAN40 | VLAN20 | * | No access to HR & Finance |
| 7 | DENY | Any | VLAN40 | VLAN51 | * | No access to Dirty Lab — critical isolation |
| 8 | DENY | Any | VLAN40 | VLAN10 | * | No access to Management infrastructure |
| 9 | DENY | Any | Any | VLAN40 | * | Default deny all other inbound |

---

#### VLAN 50 — AI Research Lab
*AI researchers and GPU cluster. Access to Dirty Lab for model transfer. No internet.*

| # | Action | Protocol | Source | Destination | Port | Purpose |
|---|--------|----------|--------|-------------|------|---------|
| 1 | PASS | TCP | VLAN50 net | VLAN30 (Wazuh) | 1514/1515 | Wazuh agent → SIEM |
| 2 | PASS | TCP | VLAN50 net | VLAN51 | 22/443 | Controlled model transfer to Dirty Lab |
| 3 | PASS | TCP | VLAN10 (Jump Server) | VLAN50 | 22/3389 | IT admin access via Jump Server only |
| 4 | DENY | Any | VLAN50 | VLAN100 | * | No internet — AI models must not phone home |
| 5 | DENY | Any | VLAN50 | VLAN20/40 | * | No access to HR or Consulting zones |
| 6 | DENY | Any | Any | VLAN50 | * | Default deny all other inbound |

---

#### VLAN 51 — Dirty Lab Sandbox
*Most restricted zone. Active exploit tools. Near-total isolation.*

| # | Action | Protocol | Source | Destination | Port | Purpose |
|---|--------|----------|--------|-------------|------|---------|
| 1 | PASS | TCP | VLAN51 net | VLAN30 (Wazuh) | 1514/1515 | Wazuh agent → SIEM — only permitted outbound |
| 2 | PASS | TCP | VLAN10 (Jump Server) | VLAN51 | 22 | IT admin access via Jump Server only |
| 3 | PASS | TCP | VLAN50 | VLAN51 | 22/443 | AI researcher model transfer |
| 4 | DENY | Any | VLAN51 | VLAN100 | * | No internet — prevents exploit tools calling home |
| 5 | DENY | Any | VLAN51 | Any | * | Block all other outbound from Dirty Lab |
| 6 | DENY | Any | Any | VLAN51 | * | Default deny all inbound except above |

---

#### VLAN 60 — Printers
*Print servers only. Receives print jobs — no outbound access needed.*

| # | Action | Protocol | Source | Destination | Port | Purpose |
|---|--------|----------|--------|-------------|------|---------|
| 1 | PASS | TCP | VLAN20/30/40 | VLAN60 | 9100 | Accept print jobs from office VLANs |
| 2 | PASS | TCP | VLAN10 | VLAN60 | 22/443 | IT admin management |
| 3 | DENY | Any | VLAN60 | Any | * | Printers have no outbound access |
| 4 | DENY | Any | Any | VLAN60 | * | Default deny all other inbound |

---

#### VLAN 70 — IoT Devices
*Untrusted devices — fully isolated. No internal access whatsoever.*

| # | Action | Protocol | Source | Destination | Port | Purpose |
|---|--------|----------|--------|-------------|------|---------|
| 1 | PASS | TCP | VLAN70 | VLAN100 | 443/80 | Internet access only (firmware updates) |
| 2 | PASS | TCP | VLAN10 | VLAN70 | 22/443 | IT admin management |
| 3 | DENY | Any | VLAN70 | Any internal | * | No access to any internal VLAN |
| 4 | DENY | Any | Any | VLAN70 | * | Default deny all inbound |

---

#### VLAN 80 — VoIP
*Voice traffic only. Strictly limited to VoIP protocols.*

| # | Action | Protocol | Source | Destination | Port | Purpose |
|---|--------|----------|--------|-------------|------|---------|
| 1 | PASS | UDP | Any VLAN | VLAN80 | 5060/5061 | SIP signaling (call setup) |
| 2 | PASS | UDP | Any VLAN | VLAN80 | 10000-20000 | RTP media streams (voice data) |
| 3 | PASS | TCP | VLAN10 | VLAN80 | 22/443 | IT admin management |
| 4 | DENY | Any | VLAN80 | Any non-VoIP | * | VoIP zone cannot initiate data connections |
| 5 | DENY | Any | Any | VLAN80 | * | Default deny all other inbound |

---

#### VLAN 100 — DMZ
*Internet-facing. Client portal and VPN gateway. Strict inbound filtering.*

| # | Action | Protocol | Source | Destination | Port | Purpose |
|---|--------|----------|--------|-------------|------|---------|
| 1 | PASS | TCP | Internet | VLAN100 (Web Portal) | 443 | HTTPS client portal access |
| 2 | PASS | UDP | Internet | VLAN100 (VPN GW) | 500/4500 | IPsec VPN — Basel branch + remote workers |
| 3 | PASS | TCP | VLAN100 | VLAN30 | 1514/1515 | Wazuh agent → SIEM |
| 4 | PASS | TCP | VLAN10 | VLAN100 | 22/443 | IT admin management |
| 5 | DENY | Any | VLAN100 | VLAN20/50/51 | * | DMZ cannot reach sensitive internal zones |
| 6 | DENY | Any | Internet | VLAN100 | 80 | Force HTTPS — no plain HTTP |
| 7 | DENY | Any | Any | VLAN100 | * | Default deny all other inbound |

---

#### VLAN 999 — Guest WiFi
*Untrusted visitors. Internet only — zero internal access.*

| # | Action | Protocol | Source | Destination | Port | Purpose |
|---|--------|----------|--------|-------------|------|---------|
| 1 | PASS | TCP/UDP | VLAN999 | Internet | 443/80/53 | HTTPS, HTTP, DNS — internet browsing only |
| 2 | DENY | Any | VLAN999 | Any internal | * | No access to any Aurora internal network |
| 3 | DENY | Any | Any | VLAN999 | * | Default deny all inbound |

---

### 3.3 Lab Implementation (Segment 2 — pfSense)

The lab simulates VLAN 30 and VLAN 40 only. Current state has allow-all rules — these will be replaced in Segment 4 with the zone-policy rules below:

| # | Action | Protocol | Source | Destination | Port | Purpose |
|---|--------|----------|--------|-------------|------|---------|
| 1 | PASS | TCP | 172.16.40.100 (Win11) | 172.16.30.10 (Wazuh) | 1514/1515 | Wazuh agent communication |
| 2 | PASS | UDP | 172.16.30.1 (pfSense) | 172.16.30.10 (Wazuh) | 514 | Syslog forwarding |
| 3 | PASS | TCP | 172.16.30.0/24 | 172.16.30.1 | 80 | pfSense web UI from VLAN30 only |
| 4 | PASS | TCP | 172.16.40.0/24 | 172.16.40.1 | 80 | pfSense web UI from VLAN40 only |
| 5 | DENY | Any | 172.16.40.0/24 | 172.16.30.0/24 | * | Block all other VLAN40 → VLAN30 traffic |
| 6 | DENY | Any | 172.16.30.0/24 | 172.16.40.0/24 | * | Block all other VLAN30 → VLAN40 traffic |

---

## 4. Device Hardening Plan

### Overview

Device hardening reduces the attack surface by disabling unused services, enforcing strong authentication, and restricting management access. This plan covers both the **production network** (Segment 1 design) and the **lab simulation** (Segment 2).

Two hardening layers are documented:
- **Production** — FortiGate NGFW, Cisco Catalyst switches (FortiOS / Cisco IOS)
- **Lab Simulation** — pfSense, Alpine Linux switches, Wazuh Ubuntu server, Windows 11

---

### 4.1 pfSense Firewall (Lab — represents FortiGate in production)

| # | Hardening Control | Action | Rationale |
|---|-------------------|--------|-----------|
| 1 | Disable HTTP management | System → Advanced → Admin Access → HTTPS only | Credentials travel in plain text over HTTP — easily intercepted via Wireshark |
| 2 | Change default admin credentials | System → User Manager → admin → change password | Default credentials are publicly known and the first thing attackers try |
| 3 | Enable SSH key-based authentication | System → Advanced → Admin Access → SSH → Authorized Keys | SSH keys cannot be phished or brute-forced like passwords |
| 4 | Restrict web UI access to VLAN 10 (Management) | Firewall → Rules → restrict admin interface to 10.10.10.0/27 only | Limits who can reach the management interface — consultants on VLAN 40 should never see the firewall UI |
| 5 | Disable UPnP | Services → UPnP → Disable | UPnP allows devices to automatically open firewall ports — dangerous in a security environment |
| 6 | Disable DHCP relay if not needed | Services → DHCP Relay → Disable | Unused services = unnecessary attack surface |
| 7 | Enable login banner | System → Advanced → Admin Access → Login Announcement | Legal requirement — warns unauthorized users before login |
| 8 | Configure NTP | Services → NTP → set to trusted NTP server | Accurate timestamps are critical for SIEM log correlation and forensics |
| 9 | Enable firewall logging | Status → System Logs → enable all categories | All traffic decisions should be logged for audit trail |
| 10 | Limit SSH access to VLAN 10 only | Firewall rule: block SSH from all VLANs except Management | SSH to the firewall from a consulting workstation should never be possible |

---

### 4.2 Alpine Linux Switches (Lab — represents Cisco Catalyst in production)

| # | Hardening Control | Action | Rationale |
|---|-------------------|--------|-----------|
| 1 | Disable password-based SSH login | `/etc/ssh/sshd_config` → `PasswordAuthentication no` | Forces key-based auth only — eliminates brute-force risk |
| 2 | Disable root SSH login | `sshd_config` → `PermitRootLogin no` | Attackers always try root first — require sudo escalation instead |
| 3 | Enable automatic security updates | `apk add apk-cron` + schedule | Unpatched systems are the most common attack vector |
| 4 | Remove unused packages | `apk del <package>` | Every installed package is a potential vulnerability |
| 5 | Set login banner | `/etc/motd` — add authorized-use warning | Legal requirement before authentication |
| 6 | Configure NTP sync | `/etc/chrony.conf` → point to internal NTP server | Log timestamps must be consistent across all devices for SIEM correlation |
| 7 | Disable unused network services | Check with `ss -tlnp` — disable anything not required | Verified in Segment 2 — port 514 UDP was the only intentional open port |

---

### 4.3 Wazuh SIEM Server — Ubuntu 22.04 (Lab & Production principle)

| # | Hardening Control | Action | Rationale |
|---|-------------------|--------|-----------|
| 1 | Disable root login via SSH | `sshd_config` → `PermitRootLogin no` | Same as Alpine — root SSH is always targeted |
| 2 | SSH key-based authentication only | `PasswordAuthentication no` in sshd_config | Already partially implemented via GitHub keys in Segment 2 |
| 3 | Enable UFW firewall | `ufw enable` — allow only ports 22, 443, 1514, 1515, 514 | UFW was disabled in Segment 2 — production should have it enabled with explicit allow rules |
| 4 | Enable automatic security updates | `apt install unattended-upgrades` | SIEM server must stay patched — it holds all audit evidence |
| 5 | Restrict Wazuh dashboard to VLAN 30 only | UFW rule: allow 443 from 172.16.30.0/24 only | Dashboard should not be reachable from Consulting VLAN |
| 6 | Enable log integrity monitoring | Wazuh File Integrity Monitoring (FIM) on `/var/ossec/logs/` | Detects Threat 5 — log deletion/tampering after privilege escalation |
| 7 | Set strong Wazuh admin password | Wazuh dashboard → Security → Users | Default credentials in Wazuh are a known risk |
| 8 | Configure NTP | `timedatectl set-ntp true` | SIEM timestamps must be accurate — all event correlation depends on this |

---

### 4.4 Windows 11 Endpoint (Lab — represents Consulting workstations in production)

| # | Hardening Control | Action | Rationale |
|---|-------------------|--------|-----------|
| 1 | Enable Windows Defender | Settings → Windows Security → enable all protection | Basic endpoint protection — first line of defence |
| 2 | Enable BitLocker full disk encryption | Control Panel → BitLocker → Enable | Protects data if device is lost or stolen — GDPR requirement for endpoint PII |
| 3 | Disable SMBv1 | PowerShell: `Set-SmbServerConfiguration -EnableSMB1Protocol $false` | SMBv1 is exploited by WannaCry and similar ransomware — no modern use case |
| 4 | Enable Windows Firewall — all profiles | Windows Security → Firewall → enable Domain/Private/Public | Already partially done in Segment 2 (ICMP rule added) |
| 5 | Disable unused services | services.msc → disable Remote Registry, Telnet, Fax | Reduces attack surface on consulting endpoints |
| 6 | Enforce screen lock after 5 minutes | Group Policy → Screen saver timeout | Physical access protection in office environment |
| 7 | Enable audit logging | Group Policy → Audit Policy → enable logon, account, object access | Windows event logs feed into Wazuh agent for SIEM monitoring |
| 8 | Wazuh agent configured to correct server IP | `ossec.conf` → `<address>172.16.30.10</address>` | Fixed in Segment 2 — was incorrectly pointing to old IP 172.16.1.10 |

---

### 4.5 Production — Cisco Catalyst 9300L / 9200L Switches

| # | Hardening Control | Cisco IOS Command | Rationale |
|---|-------------------|-------------------|-----------|
| 1 | Disable unused physical ports | `interface GigX/Y` → `shutdown` | Unused open ports allow rogue device connection |
| 2 | Enable SSH, disable Telnet | `line vty 0 4` → `transport input ssh` | Telnet sends credentials in plain text |
| 3 | Encrypt stored passwords | `service password-encryption` | Prevents reading passwords from config backup files |
| 4 | Disable CDP on external-facing ports | `no cdp run` or per-interface `no cdp enable` | CDP leaks device model and IOS version to attackers — reconnaissance tool |
| 5 | Disable unused VLAN interfaces | `interface vlanX` → `shutdown` | Reduces L3 attack surface on core switch SVIs |
| 6 | Restrict VTY access to VLAN 10 | `access-class <ACL> in` on `line vty 0 4` | Only management VLAN should SSH to switches |
| 7 | Set login banner | `banner login ^Authorized access only^` | Legal warning before authentication |
| 8 | Enable logging to SIEM | `logging host 10.10.30.x` (Splunk/Sentinel IP) | All switch events forwarded to SIEM for audit trail |
| 9 | Configure NTP | `ntp server 10.10.30.x` | Consistent timestamps across all devices |
| 10 | Secure SNMP | `snmp-server community <strong> RO <ACL>` or disable if unused | Default SNMP community strings ("public") are widely known |
| 11 | Enable Port Security on access ports | `switchport port-security maximum 2` | Limits MAC addresses per port — prevents rogue device connection |
| 12 | Enable DHCP Snooping | `ip dhcp snooping vlan 40` | Prevents rogue DHCP server attack (Threat from the STRIDE list) |

---

### 4.6 Production — FortiGate NGFW (represents pfSense in lab)

| # | Hardening Control | Action | Rationale |
|---|-------------------|--------|-----------|
| 1 | Disable HTTP admin access | System → Settings → Administration → HTTPS only | Same principle as pfSense — no plain text management |
| 2 | Enable MFA for admin accounts | System → Administrators → enable TOTP/Hardware Key | FortiGate admin access must require second factor — NIS2 requirement |
| 3 | Restrict admin access to VLAN 10 | Firewall policy: deny admin interface from all except 10.10.10.0/27 | Management plane isolation |
| 4 | Enable IPS/IDS signatures | Security Profiles → IPS → enable and apply to policies | FortiGate has built-in IPS — detect exploit attempts in real time |
| 5 | Enable Web Application Firewall | Security Profiles → WAF → apply to DMZ VLAN 100 | Protects client portal against SQL injection, XSS, Threat 4 |
| 6 | Configure syslog to SIEM | Log → Log Settings → remote syslog → Splunk/Sentinel IP | All firewall events forwarded to SIEM |
| 7 | Set strong admin password + rename admin account | System → Administrators | Default "admin" username is always targeted |
| 8 | Enable HA heartbeat encryption | System → HA → encrypt heartbeat | Prevents HA pair communication from being intercepted or spoofed |

---

---

## 5. Logging & Monitoring Plan

### Overview

Centralized logging is a core requirement under NIS2 (Art. 23 — incident reporting), IT-SiG 2.0 (§8a BSIG — detection systems), and NIST CSF Detect function. All log sources forward to the SIEM in VLAN 30, where events are correlated, alerted, and retained for a minimum of 6 months.

---

### 5.1 Log Sources

| Category | Device / Source | Log Type | Collection Method |
|----------|----------------|----------|-------------------|
| **Network Infrastructure** | Cisco Catalyst 9300L / 9200L switches | Interface events, MAC table changes, port security violations | Syslog UDP 514 → SIEM |
| **Network Infrastructure** | FortiGate NGFW (HQ + Basel) | Firewall allow/deny, IPS alerts, VPN events, admin logins | Syslog UDP 514 → SIEM |
| **Network Infrastructure** | Wireless Controller | Client associations, deauthentications, rogue AP detection | Syslog UDP 514 → SIEM |
| **Security Devices** | FortiGate IPS/IDS | Exploit attempts, signature matches, anomaly detection | Built-in FortiGate logging → SIEM |
| **Security Devices** | WAF (DMZ VLAN 100) | SQL injection attempts, XSS, brute force on client portal | Syslog → SIEM |
| **Servers** | ZTNA / Jump Server (VLAN 30) | Admin login attempts, privilege escalation, session activity | Wazuh agent |
| **Servers** | HR Database Server (VLAN 20) | Query logs, access attempts, schema changes | Wazuh agent |
| **Servers** | NTP Server (VLAN 30) | Time sync status, client requests | Syslog → SIEM |
| **Servers** | VPN Gateway (VLAN 100) | Connection attempts, auth failures, session duration | Syslog → SIEM |
| **Endpoints** | All Windows workstations (VLAN 40) | Logon/logoff, process creation, file changes, network connections | Wazuh agent (TCP 1514) |
| **Applications** | Client Web Portal (VLAN 100) | HTTP access logs, auth failures, API calls | Syslog / Filebeat → SIEM |
| **OT / IoT** | HVAC & Environmental Sensors (VLAN 71) | Status changes, anomalous readings | Syslog → SIEM |
| **Lab (Segment 2)** | pfSense_Aurora | Firewall events, DHCP leases, auth | Syslog UDP 514 → Wazuh ✅ operational |
| **Lab (Segment 2)** | Win11-Target Wazuh Agent | Logon events, process activity, file integrity | Wazuh agent TCP 1514 ✅ operational |

---

### 5.2 Log Collection Architecture

```
[All log sources]
        │
        ├── Wazuh Agents (TCP 1514/1515) ── endpoints & servers
        └── Syslog UDP 514 ──────────────── network devices & firewalls
                │
                ▼
        Wazuh Manager (VLAN 30 — 172.16.30.10)
        - Receives, parses, and correlates all events
        - Applies detection rules and generates alerts
                │
                ▼
        Wazuh Indexer (OpenSearch)
        - Stores and indexes all events
        - Supports search and dashboard queries
                │
                ▼
        Immutable Backup NAS (VLAN 30)
        - Write-once storage for 6-month NIS2 retention
        - Cannot be modified or deleted — mitigates Threat 5
```

---

### 5.3 Retention Policy

| Requirement | Minimum Retention | Storage Location | Notes |
|-------------|------------------|------------------|-------|
| NIS2 Art. 23 | 6 months | Immutable NAS (VLAN 30) | Mandatory for EU operators of essential services |
| IT-SiG 2.0 §8a | 6 months | Immutable NAS (VLAN 30) | German critical infrastructure requirement |
| GDPR Art. 32 | As long as necessary | Encrypted NAS | PII in logs must be protected and purged when no longer needed |
| Internal SOC | 12 months (recommended) | SIEM active index | Extended retention enables threat hunting and retrospective analysis |

---

### 5.4 Key Alerting Rules

| Alert | Trigger | Severity | STRIDE Threat |
|-------|---------|----------|---------------|
| Multiple failed logins | ≥5 failures in 60 seconds on any account | High | Threat 1 — phishing/credential attack |
| SIEM service stopped | wazuh-manager / wazuh-indexer stops unexpectedly | Critical | Threat 5 — log deletion attempt |
| Log file modified | File integrity change in `/var/ossec/logs/` | Critical | Threat 5 — tampering with audit trail |
| VLAN 51 outbound connection | Any traffic from 10.10.51.0/24 not in approved list | Critical | Threat 2 — Dirty Lab sandbox escape |
| DMZ portal auth failures | ≥10 failures in 5 minutes on client portal | High | Threat 4 — client data theft attempt |
| IPsec VPN tunnel down | FortiGate reports tunnel failure | High | Threat 3 — DDoS / connectivity disruption |
| New admin account created | Any new privileged account creation | Medium | Privilege escalation detection |
| Off-hours admin login | Admin login outside 07:00–20:00 CET | Medium | Insider threat / compromised credentials |

---

### 5.5 Dashboard Requirements

The SIEM dashboard should provide the following views for the SOC team:

| Dashboard | Purpose |
|-----------|---------|
| **Security Overview** | Real-time alert summary by severity — Critical / High / Medium / Low |
| **Agent Status** | All Wazuh agents — Active / Disconnected — instant visibility of monitoring gaps |
| **Authentication Monitor** | Failed login attempts across all systems — detect brute force and credential attacks |
| **Network Traffic** | Firewall allow/deny summary per VLAN — detect unusual cross-VLAN traffic |
| **VLAN 51 Activity** | Dedicated Dirty Lab monitoring — any anomalous outbound connection triggers immediate alert |
| **Compliance View** | Log retention status, GDPR data handling, NIS2 reporting readiness |

*In the lab (Segment 2), the Wazuh dashboard is operational at https://172.16.30.10 — the Security Overview and Agent Status views are already functional.*

---

---

## 6. SOC Roles & Workflow Definition

### 6.1 SOC Structure

Aurora AI Security operates a small internal SOC appropriate for a startup-stage consultancy. Given resource constraints, Tier 1 and Tier 2 analyst functions are combined into a single Security Analyst role. The SOC scales to dedicated tiers as the company grows toward ISO 27001 certification.

| Tier | Role | Assigned To | Core Responsibilities |
|------|------|-------------|----------------------|
| **SOC Manager** | Compliance & Governance Lead | Morgan (Compliance Officer) | SOC oversight, NIS2 regulatory reporting, audit trail management, escalation decisions, automated compliance reporting |
| **Tier 1/2** | Security Analyst (x2 minimum) | Dedicated hire | Alert triage, initial investigation, incident containment, Wazuh dashboard monitoring, false positive management |
| **Tier 3** | Senior Security Engineer | IT Lead / Senior hire | Proactive threat hunting, detection rule development, malware analysis, Dirty Lab oversight, SIEM tuning |

> **Coverage Note:** A minimum of 2 Security Analysts are required to ensure continuous SOC coverage. On-call rotation covers after-hours and weekends. Critical alerts trigger automated SMS/email notifications via Wazuh to the on-call analyst and SOC Manager regardless of shift.

---

### 6.2 Alert Triage Workflow

```
[Wazuh generates alert]
         │
         ▼
[Tier 1/2 Analyst reviews alert in dashboard]
         │
         ├── False positive → Document → Close → Tune rule to reduce noise
         │
         ├── Low/Medium severity → Investigate → Remediate → Document
         │
         └── High/Critical severity → Escalate to Tier 3 + notify SOC Manager
                    │
                    ▼
         [Tier 3 deep investigation]
                    │
                    ├── Contained → Eradicate → Recover → Post-incident report
                    │
                    └── Significant incident → Trigger IR Playbook → NIS2 reporting
```

---

### 6.3 Internal SLA — Response Times

| Alert Severity | Wazuh Rule Level | Response SLA | Action Required |
|----------------|-----------------|--------------|-----------------|
| **Critical** | Level 15+ | 15 minutes | Immediate escalation to Tier 3 + SOC Manager. Potential NIS2 reporting. |
| **High** | Level 12–14 | 1 hour | Tier 1/2 investigates. Escalate if not resolved within SLA. |
| **Medium** | Level 7–11 | 4 hours | Tier 1/2 investigates during shift. Document findings. |
| **Low** | Level 0–6 | 24 hours | Review during next shift. Tune if recurring false positive. |

---

### 6.4 Regulatory Reporting Obligations

| Obligation | Trigger | Deadline | Responsible | Method |
|------------|---------|----------|-------------|--------|
| NIS2 Early Warning (Art. 23) | Significant incident detected | **24 hours** from awareness | SOC Manager (Morgan) | BSI MELDESTELLE portal |
| NIS2 Full Notification (Art. 23) | Confirmed significant incident | **72 hours** from awareness | SOC Manager (Morgan) | Detailed impact report to BSI |
| GDPR Breach Notification (Art. 33) | Personal data involved in breach | **72 hours** from awareness | Data Protection Officer | Supervisory authority notification |
| Internal Post-Incident Report | Any High/Critical incident | **5 business days** | Tier 3 Analyst | Internal documentation + lessons learned |

> **Automation:** Wazuh + Eramba GRC (planned) will provide automated deadline tracking and report generation for NIS2 and GDPR obligations, reducing manual compliance burden and risk of missed reporting windows.

---

### 6.5 SOC Functions

#### Alert Triage (Tier 1/2)
- Monitor Wazuh dashboard continuously during business hours
- Classify incoming alerts by severity and STRIDE threat category
- Investigate Low/Medium alerts independently
- Escalate High/Critical alerts within SLA
- Document all actions in ticketing system
- Tune Wazuh rules to reduce false positive rate

#### Incident Response (Tier 1/2 + Tier 3)
- Contain affected systems (isolate VM, block firewall rule)
- Preserve evidence — log snapshots before any remediation
- Perform root cause analysis
- Eradicate threat and restore to known-good state
- Trigger NIS2/GDPR reporting if threshold met
- Produce post-incident report within 5 business days

#### Threat Hunting (Tier 3)
- Proactive weekly review of Wazuh logs for indicators of compromise (IOCs) not caught by automated rules
- Develop custom Wazuh detection rules based on MITRE ATT&CK techniques relevant to Aurora
- Monitor threat intelligence feeds for new CVEs affecting Aurora's infrastructure (pfSense, Ubuntu, Windows 11, FortiGate)
- Quarterly review of firewall rules and ACLs — remove unused rules, tighten existing ones
- Oversee Dirty Lab (VLAN 51) activity — review all researcher sessions

---

---

## 7. Incident Response Playbook

### 7.1 IR Lifecycle — Standard Phases

All incidents at Aurora follow the 5-phase IR lifecycle regardless of threat type:

| Phase | Description | Responsible |
|-------|-------------|-------------|
| **1. Detection** | Wazuh alert triggers, analyst reviews logs to confirm real incident vs. false positive | Tier 1/2 Analyst |
| **2. Containment** | Stop the spread — isolate affected system, revoke compromised credentials/sessions, block attack vector | Tier 1/2 + Tier 3 |
| **3. Eradication** | Remove the threat — delete malware, close attack vector, assess blast radius (what systems/data were accessed) | Tier 3 |
| **4. Recovery** | Restore normal operations — re-image if necessary, restore from backup, verify clean state, return user to network | Tier 1/2 + Tier 3 |
| **5. Lessons Learned** | Post-incident report within 5 business days — root cause, timeline, blast radius, regulatory obligations, Wazuh rule updates | SOC Manager + Tier 3 |

> **Evidence Preservation Rule:** Before any containment or eradication action, take a snapshot of logs and system state. Modifying a system before preserving evidence destroys forensic value and may violate NIS2 reporting requirements.

---

### 7.2 Playbook 1 — Social Engineering / Phishing (Threat 1)

**Trigger:** Wazuh alert — multiple failed logins, anomalous login time/location, or user reports suspicious email.

| Phase | Actions |
|-------|---------|
| **Detection** | Review Wazuh authentication logs. Confirm credential theft vs. false positive. Check login source IP and time against user's normal pattern. |
| **Containment** | 1. Immediately revoke active sessions and force re-authentication. 2. Disable compromised account temporarily. 3. Block source IP at FortiGate (temporary — attacker may rotate IPs). 4. Isolate affected workstation from VLAN 40 — move to quarantine VLAN. 5. Notify user and SOC Manager. |
| **Eradication** | 1. Reset credentials with strong password + new MFA token. 2. Assess blast radius — what did the attacker access with stolen credentials? Check VLAN 40 file access logs and cross-VLAN connections. 3. Scan isolated workstation for malware/keyloggers. 4. Check if attacker attempted lateral movement to VLAN 20 or VLAN 50. |
| **Recovery** | 1. Re-image workstation if malware found. 2. Restore user access with new credentials and verified MFA. 3. Return workstation to VLAN 40. 4. Monitor account closely for 2 weeks post-incident. |
| **Lessons Learned** | 1. Document full timeline and blast radius. 2. Assess NIS2/GDPR reporting obligation (was client/employee PII accessed?). 3. Update Wazuh rules to detect similar patterns earlier. 4. Schedule targeted phishing awareness training for affected department. |

---

### 7.3 Playbook 2 — Dirty Lab Sandbox Escape (Threat 2)

**Trigger:** Wazuh alert — VLAN 51 outbound connection to non-approved destination, or unexpected process on VLAN 30/40 originating from VLAN 51 source.

| Phase | Actions |
|-------|---------|
| **Detection** | Review Wazuh VLAN 51 activity logs. Confirm unexpected outbound traffic or lateral movement attempt. Check FortiGate firewall logs for VLAN 51 rule violations. |
| **Containment** | 1. Immediately shut down all VLAN 51 network connectivity at FortiGate — full isolation. 2. Notify Tier 3 and SOC Manager immediately (Critical severity). 3. Preserve all VLAN 51 logs before any changes. 4. Identify which Dirty Lab node is involved. |
| **Eradication** | 1. Identify the escaped exploit tool or compromised node. 2. Assess blast radius — did any VLAN 30/40 systems receive unexpected connections? 3. Check if client AI model data was exfiltrated. 4. Remove compromised node from Dirty Lab environment. |
| **Recovery** | 1. Restore Dirty Lab from known-good snapshot. 2. Re-enable VLAN 51 connectivity only after full investigation. 3. Notify affected clients if their AI model data was involved. 4. Review and tighten VLAN 51 egress rules. |
| **Lessons Learned** | 1. Full post-incident report — mandatory NIS2 notification if client data involved. 2. Review Dirty Lab access controls and researcher session logging. 3. Update firewall rules to prevent recurrence. 4. Consider additional network segmentation within VLAN 51. |

---

### 7.4 Playbook 3 — DDoS Attack on IPsec VPN Tunnel (Threat 3)

**Trigger:** FortiGate alert — IPsec tunnel down, or Wazuh alert — Basel branch agents disconnected simultaneously.

| Phase | Actions |
|-------|---------|
| **Detection** | Confirm tunnel is down via FortiGate HA pair status. Verify it is a DDoS (volumetric traffic) vs. configuration issue. Check ISP for upstream attack confirmation. |
| **Containment** | 1. Activate FortiGate HA failover if not automatic. 2. Contact ISP — request upstream DDoS scrubbing. 3. Enable LTE failover for Basel branch (backup connectivity). 4. Notify Basel branch staff of disruption and ETA. 5. Assess SLA breach risk for active client engagements. |
| **Eradication** | DDoS is external — eradication depends on ISP scrubbing or attack subsiding. Continue monitoring traffic volume. Rate limiting on VPN gateway to reduce impact of future attacks. |
| **Recovery** | 1. Restore IPsec tunnel once attack subsides. 2. Verify Basel branch connectivity — confirm all agents reconnect to Wazuh. 3. Document downtime duration for SLA review. |
| **Lessons Learned** | 1. Calculate business impact — hours of downtime × consultant billing rate. 2. Review ISP DDoS protection contract. 3. Document SLA breach if applicable — notify affected clients. 4. Consider redundant ISP connection for Basel branch. |

---

### 7.5 Playbook 4 — Client Data Theft via DMZ Web Portal (Threat 4)

**Trigger:** Wazuh/WAF alert — multiple authentication failures on client portal, unusual data download volume, or anomalous API calls from DMZ VLAN 100.

| Phase | Actions |
|-------|---------|
| **Detection** | Review WAF and Wazuh logs for VLAN 100. Identify source IP, targeted account, and data accessed. Determine if attacker authenticated as legitimate client (Spoofing) or exploited vulnerability. |
| **Containment** | 1. Immediately disable compromised client portal account. 2. Block source IP at FortiGate perimeter. 3. Take portal offline if active exploitation is confirmed. 4. Preserve all portal access logs. |
| **Eradication** | 1. Identify and patch exploited vulnerability. 2. Assess blast radius — which client reports were accessed? 3. Review all portal accounts for signs of unauthorized access. 4. Reset all portal credentials as precaution. |
| **Recovery** | 1. Apply patch and security test portal before bringing back online. 2. Notify affected clients — mandatory if their confidential data was accessed. 3. Restore portal with enhanced WAF rules. 4. Implement client-side MFA on portal if not already active. |
| **Lessons Learned** | 1. GDPR Art. 33 notification if EU client PII was accessed (72-hour deadline). 2. NIS2 notification if significant incident threshold met. 3. Commission penetration test of portal post-incident. 4. Review data isolation — each client must only see their own reports. |

---

### 7.6 Playbook 5 — Log Deletion After Privilege Escalation (Threat 5)

**Trigger:** Wazuh File Integrity Monitoring (FIM) alert — modification or deletion detected in `/var/ossec/logs/`, or Wazuh service unexpectedly stopped.

| Phase | Actions |
|-------|---------|
| **Detection** | FIM alert triggers immediately on log tampering. Review which logs were modified/deleted and from which account. Cross-reference with immutable NAS backup to identify gap. |
| **Containment** | 1. Immediately revoke admin account that performed deletion. 2. Isolate Wazuh server from network — prevent further tampering. 3. Preserve immutable NAS backup — this is now primary evidence. 4. Notify SOC Manager — this indicates a prior breach that attacker is now covering up. |
| **Eradication** | 1. Investigate how admin account was compromised — trace back to original breach (likely Threat 1 or Threat 4). 2. Identify full timeline using NAS backup logs. 3. Assess what actions the attacker performed that they were trying to hide. 4. Remove attacker access from all systems. |
| **Recovery** | 1. Restore deleted logs from immutable NAS backup. 2. Rebuild Wazuh server if compromised. 3. Reset all admin credentials. 4. Re-enable monitoring and verify FIM is active. |
| **Lessons Learned** | 1. NIS2 mandatory notification — log deletion likely means a prior significant incident occurred. 2. Update Wazuh rules — alert on any admin account accessing log directories. 3. Review privileged access controls — implement HashiCorp Vault for time-limited admin credentials. 4. Verify NAS immutability — confirm backup integrity was maintained. |

---

---

## 8. Implementation Roadmap

### Overview

The roadmap sequences all security controls, monitoring setup, and SOC onboarding across 4 phases aligned with the Aurora project segments. Controls are prioritized by risk score — High risk threats (Score 12) are addressed first.

---

### 8.1 Roadmap Table

| Phase | Timeline | Control / Activity | Priority | Status | Segment |
|-------|----------|--------------------|----------|--------|---------|
| **Phase 1 — Foundation** | Feb–Mar 2026 | Network design — VLAN scheme, IP addressing, compliance mapping | Critical | ✅ Complete | Seg. 1 |
| | | Hardware selection — FortiGate, Cisco Catalyst, pfSense | Critical | ✅ Complete | Seg. 1 |
| | | Security framework selection — NIST CSF 2.0 | Critical | ✅ Complete | Seg. 1 |
| **Phase 2 — Infrastructure** | Mar–Apr 2026 | Deploy virtualized lab — pfSense, Wazuh, Alpine switches, Win11, Kali | Critical | ✅ Complete | Seg. 2 |
| | | VLAN segmentation — separate UTM bridges per VLAN | Critical | ✅ Complete | Seg. 2 |
| | | Inter-VLAN routing — pfSense LAN + VLAN40CONSULTING | Critical | ✅ Complete | Seg. 2 |
| | | Wazuh SIEM deployment — Manager + Indexer + Dashboard | Critical | ✅ Complete | Seg. 2 |
| | | Wazuh agent — Windows 11 endpoint | High | ✅ Complete | Seg. 2 |
| | | pfSense syslog → Wazuh UDP 514 | High | ✅ Complete | Seg. 2 |
| **Phase 3 — Security Controls** | Apr 2026 | STRIDE threat model & risk matrix | Critical | ✅ Complete | Seg. 3 |
| | | Firewall ACL design — default-deny, all 11 VLANs | Critical | ✅ Complete | Seg. 3 |
| | | Device hardening plan — pfSense, Alpine, Wazuh, Win11, Cisco, FortiGate | High | ✅ Complete | Seg. 3 |
| | | Logging & monitoring plan — log sources, retention, alerting rules | High | ✅ Complete | Seg. 3 |
| | | SOC roles & workflow — tiers, SLAs, NIS2 reporting | High | ✅ Complete | Seg. 3 |
| | | Incident response playbooks — 5 threat-specific playbooks | High | ✅ Complete | Seg. 3 |
| **Phase 4 — Implementation & Validation** | Apr–May 2026 | Deploy zone-policy firewall rules in pfSense (replace allow-all) | Critical | 🔜 Seg. 4 | Seg. 4 |
| | | Apply device hardening — pfSense, Alpine, Wazuh, Win11 | High | 🔜 Seg. 4 | Seg. 4 |
| | | Configure Wazuh alerting rules — map to 5 STRIDE threats | High | 🔜 Seg. 4 | Seg. 4 |
| | | Configure Wazuh dashboards — Security Overview, Auth Monitor, VLAN 51 | Medium | 🔜 Seg. 4 | Seg. 4 |
| | | Penetration testing — Kali Linux external attack simulation | High | 🔜 Seg. 4 | Seg. 4 |
| | | Validate detection — confirm Wazuh generates alerts during attack | High | 🔜 Seg. 4 | Seg. 4 |
| | | SOC workflow simulation — triage, escalate, contain, document | Medium | 🔜 Seg. 4 | Seg. 4 |
| | | Post-implementation review & final portfolio documentation | Medium | 🔜 Seg. 4 | Seg. 4 |

---

### 8.2 Risk-Prioritized Control Sequence

Controls are sequenced by risk score from Section 1 — highest risk threats addressed first in Phase 4:

| Priority | Threat | Risk Score | Control Implemented In |
|----------|--------|------------|----------------------|
| 1 | Social Engineering / Phishing | 12 | MFA enforcement, VLAN 40 ACLs, Wazuh auth alerts |
| 1 | DDoS on IPsec VPN | 12 | FortiGate HA, ISP scrubbing, LTE failover |
| 3 | Dirty Lab Sandbox Escape | 8 | VLAN 51 strict egress rules, FIM monitoring |
| 3 | Client Data Theft via DMZ | 8 | WAF on VLAN 100, portal MFA, patching |
| 3 | Log Deletion / Privilege Escalation | 8 | Wazuh FIM on logs, immutable NAS, HashiCorp Vault |

---

### 8.3 Extension Controls (Post-Segment 4)

| Control | Tool | Purpose | Timeline |
|---------|------|---------|----------|
| Intrusion Detection | Suricata (pfSense package) | Inline IDS/IPS — detects Kali attack patterns in real time | Post-Seg. 4 |
| Privileged Access Management | HashiCorp Vault | Time-limited admin credentials — mitigates Threat 5 | Post-Seg. 4 |
| SOC Automation | Python scripts | Alert parsing, automated response, health checks | Post-Seg. 4 |
| GRC Management | Eramba Community | Maps NIST CSF controls to ISO 27001 / SOC 2 — NIS2 reporting automation | May 2026 |
