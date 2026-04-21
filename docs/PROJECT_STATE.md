# PROJECT STATE - Aurora AI Security

**Last Updated:** April 12, 2026  
**Student:** Bernd Janzen  
**Program:** Masterschool Cybersecurity Bootcamp  
**Timeline:** February - May 2026 (~12 weeks total)

---

## ⚠️ CRITICAL: EXISTING INFRASTRUCTURE

**DO NOT suggest starting from scratch!**
**DO NOT suggest VirtualBox/VMware!**
**DO NOT suggest downloading new VMs!**

**CURRENT STATE (April 12):**
- ✅ 8 VMs deployed and renamed in UTM with VLAN prefix
- ✅ pfSense_Aurora: 3 NICs — em0=WAN, em1=VLAN30 (172.16.30.1), em2=VLAN40 (172.16.40.1)
- ✅ Wazuh SIEM at 172.16.30.10 (VLAN30 — IT & Infrastructure)
- ✅ Win11 at 172.16.40.100 (VLAN40 — Consulting)
- ✅ Inter-VLAN routing working (Win11 ↔ Wazuh confirmed, 0% loss)
- ✅ pfSense web UI accessible from both VLANs (http://172.16.30.1 / http://172.16.40.1)
- 🔜 Switch-2-Alpine: needs IP reconfigured to 172.16.30.11
- 🔜 Switch-1-Alpine: needs IP reconfigured to 172.16.40.11
- 🔜 Kali at 192.168.64.x (WAN/external — attacker, no agent)

**ARCHITECTURE DECISION (April 10):**
Proper separate VLAN interfaces via UTM bridges — NOT flat network.
- UTM "Default (private)" bridge → VLAN30 (IT & Infrastructure) → pfSense em1
- UTM "Network 0" bridge → VLAN40 (Consulting) → pfSense em2
- Traffic between VLANs routes through pfSense — real inter-VLAN enforcement
- Kali on WAN side (external attacker) — VLAN51 Dirty Lab not simulated (resource constraints)

**REMAINING FOR SEGMENT 2:**
1. Reconfigure Switch-2 IP to 172.16.30.11 (Alpine setup-interfaces)
2. Reconfigure Switch-1 IP to 172.16.40.11 (Alpine setup-interfaces)
3. Replace allow-all rules with proper zone-policy firewall rules
4. Configure pfSense syslog → Wazuh 172.16.30.10 UDP 514 (extension credit)
5. Verify Wazuh agent on Win11 still active
6. Compile PDF: Segment2_VirtualBuild_Janzen.pdf

---

## Project Overview

**Fictional Company:** Aurora AI Security GmbH  
**Objective:** Complete enterprise network design and security infrastructure implementation demonstrating:
- Network architecture & VLAN segmentation
- SIEM deployment & security monitoring
- Compliance framework implementation (GDPR, NIS2, IT-SiG, FADP)
- SOC workflow design & incident response
- Penetration testing & security validation

---

## Infrastructure

**Host Environment:**
- MacBook Air M3 (16GB RAM)
- UTM Hypervisor (QEMU/Apple Virtualization)
- VLAN30: 172.16.30.0/24 (UTM "Default private" Host Only bridge)
- VLAN40: 172.16.40.0/24 (UTM "Network 0" Host Only bridge)
- WAN: 192.168.64.0/24 (UTM Shared Network/NAT)

**Virtual Machines (8 total):**

| UTM VM Name | OS | Arch | VLAN | Role | IP | Status |
|---|---|---|---|---|---|---|
| pfSense_Aurora | pfSense 2.7.2 | AMD64 | WAN+30+40 | Router/Firewall/DHCP | em0:192.168.64.11 / em1:172.16.30.1 / em2:172.16.40.1 | ✅ Running |
| VLAN30-Wazuh-Server-Ubuntu 22.04 AMD | Ubuntu 22.04 | AMD64 | VLAN30 | SIEM | 172.16.30.10 | ✅ Operational |
| VLAN30-Switch-2-Alpine | Alpine 3.23.3 | ARM64 | VLAN30 | Virtual L2 Switch | 172.16.30.11 🔜 | Needs IP reconfigure |
| VLAN40-Win11-Target | Windows 11 Pro | ARM64 | VLAN40 | Target endpoint | 172.16.40.100 | ✅ Configured |
| VLAN40-Switch-1-Alpine | Alpine 3.23.3 | ARM64 | VLAN40 | Virtual L2 Switch | 172.16.40.11 🔜 | Needs IP reconfigure |
| WAN-Kali Linux 2024-Attacker | Kali Linux | ARM64 | WAN | External attacker | 192.168.64.x (DHCP) | ✅ Ready |
| VLAN30-AdminWS-Ubuntu 25.10 ARM | Ubuntu 25.10 | ARM64 | N/A | Optional — Apple Virt only (no Host Only) | TBD | Not in VLAN design |
| BACKUP-Ubuntu 22.04 AMD clone | Ubuntu 22.04 | AMD64 | N/A | Backup/Testing | N/A | Backup only |

---

## Segment Status

### ✅ Segment 1: Network Planning (COMPLETE)
- **Duration:** 2 weeks  
- **Deliverable:** 10-page network design PDF  
- **Key Components:**
  - VLAN design (VLAN 10–999)
  - IP addressing scheme (10.10.x.x Freiburg HQ, 10.11.x.x Basel branch)
  - NIST CSF framework selection & justification
  - Compliance mapping (GDPR, NIS2, IT-SiG 2.0, FADP, CRA)
  - Risk assessment matrix
- **Status:** Submitted and approved

### ✅ Segment 2: Virtual Infrastructure Build (COMPLETE — PDF pending submission)
- **Deadline:** April 11, 2026
- **Completed:**
  - ✅ 8 VMs deployed in UTM, renamed with VLAN prefix
  - ✅ Proper VLAN interfaces via separate UTM bridges
  - ✅ pfSense: WAN + VLAN30 (172.16.30.1) + VLAN40 (172.16.40.1)
  - ✅ Wazuh SIEM operational at 172.16.30.10
  - ✅ Wazuh agent on Windows 11 — Active (reconfigured to 172.16.30.10 after VLAN migration)
  - ✅ Inter-VLAN routing confirmed (Win11 ↔ Wazuh, 0% packet loss)
  - ✅ pfSense firewall rules: pass rules for LAN + VLAN40CONSULTING
  - ✅ Switch-2 IP → 172.16.30.11 ✅
  - ✅ Switch-1 IP → 172.16.40.11 ✅
  - ✅ pfSense syslog → Wazuh UDP 514 (confirmed via tcpdump — extension credit)
  - ✅ 15 screenshots taken and named
  - ✅ HTML document built: Segment2_VirtualBuild_Janzen.html (print to PDF)
- **Remaining:**
  - 🔜 Print HTML → Segment2_VirtualBuild_Janzen.pdf and submit
  - 🔜 Zone-policy firewall rules (deferred to Segment 3 design, implemented in Segment 4)

### 🔄 Segment 3: Security & SOC Planning (IN PROGRESS)
- **Started:** April 14, 2026
- **Deadline:** ~April 28, 2026
- **Deliverable:** Segment3_SecuritySOCPlan_Janzen.pdf (≤14 pages)
- **Completed:**
  - ✅ Threat modeling (STRIDE) — 5 threats identified and scored
  - ✅ Framework justification (NIST CSF 2.0, ~350 words, regulatory mapping GDPR/NIS2/IT-SiG/FADP)
  - ✅ Device hardening plan — 6 device types (pfSense, Alpine, Wazuh, Win11, Cisco, FortiGate)
  - ✅ Logging & monitoring plan — log sources, collection architecture, retention, alerting rules, dashboards
- **Remaining:**
  - ✅ Firewall rules & ACL tables per VLAN zone (Section 3) — all 11 VLANs + lab rules
  - ✅ SOC roles & workflow definition (Section 6) — tiers, SLAs, NIS2 reporting, threat hunting
  - ✅ Incident response playbook (Section 7) — 5 threat-specific playbooks
  - ✅ Implementation roadmap (Section 8) — 4 phases, risk-prioritized
  - 🔜 Build HTML → compile PDF: Segment3_SecuritySOCPlan_Janzen.pdf

### 📅 Segment 4: Implementation & Validation (FINAL)
- **Duration:** 2 weeks (starts ~April 28)
- **Focus:**
  - Deploy security controls in pfSense
  - Configure SIEM alerts & dashboards
  - Penetration testing from Kali (external attack simulation)
  - SOC workflow simulation
- **Deliverable:** Final validation PDF (capstone)

---

## Technical Decisions & Learnings

**Key Issues Resolved:**
1. **Ubuntu 24.04 AMD64 incompatible** with Wazuh under Rosetta 2 (Java JVM segfaults)
   - **Solution:** Downgraded to Ubuntu 22.04 AMD64 ✅

2. **UTM does not support 802.1Q VLAN tagging**
   - **Solution:** Separate UTM Host Only bridges per VLAN (one bridge per segment) ✅
   - Each bridge = isolated L2 network; pfSense gets one NIC per bridge

3. **Wazuh Dashboard couldn't connect to Indexer**
   - **Root cause:** HTTP vs HTTPS mismatch in opensearch_dashboards.yml
   - **Solution:** Changed to `https://172.16.30.10:9200` ✅

4. **Wazuh API timeout errors**
   - **Solution:** Increased timeout from 20,000ms to 60,000ms in dashboard config ✅

5. **pfSense default LAN pass rules deleted accidentally**
   - **Solution:** Re-added via web UI using pfctl -d + easyrule approach ✅

6. **Ubuntu 25.10 ARM uses Apple Virtualization Framework — no Host Only network**
   - **Impact:** Cannot be placed on VLAN30 bridge; excluded from VLAN design
   - **Mitigation:** Mac host manages pfSense/Wazuh directly (admin access)

**Architecture Notes:**
- ARM vs AMD64 matters: Win11/Kali/Ubuntu 25.10 are ARM native; pfSense/Alpine/Wazuh are AMD64 emulated
- Alpine Wazuh packages are x86_64 only — no ARM64 build → pfSense syslog forwarding is the mitigation
- VLAN30 = IT & Infrastructure (maps to production VLAN 30)
- VLAN40 = Consulting (maps to production VLAN 40)
- Kali = external attacker on WAN; VLAN51 Dirty Lab Sandbox not simulated in minimal lab

---

## Wazuh SIEM Configuration

**Version:** Wazuh 4.14.3 (all-in-one installation)

**Components:**
- **Wazuh Manager:** Log collection, analysis, alerting
- **Wazuh Indexer:** Data storage (OpenSearch-based)
- **Wazuh Dashboard:** Web UI (port 443)
- **Filebeat:** Log shipper

**Service Status:**
```bash
systemctl status wazuh-manager    # Active
systemctl status wazuh-indexer    # Active  
systemctl status wazuh-dashboard  # Active
systemctl status filebeat         # Active
```

**Credentials:** Stored in `/root/wazuh-install-files.tar`

**Dashboard Access:** https://172.16.30.10

**Configuration Files:**
- Manager: `/var/ossec/etc/ossec.conf`
- Dashboard: `/etc/wazuh-dashboard/opensearch_dashboards.yml`
- Indexer: `/etc/wazuh-indexer/opensearch.yml`

---

## Workflow Setup

**Version Control:**
- **Repo:** https://github.com/manjou/aurora-ai-security
- **Local Path:** ~/Documents/Aurora-AI-Security
- **Branch:** main

**Git Configuration:**
```
user.name=Bernd Janzen
user.email=manju.janzen@googlemail.com
```

**Development Environment:**
- **IDE:** VS Code
- **AI Tools:** Claude Code (terminal), GitHub Copilot (inline)
- **Terminal:** Integrated in VS Code

**Daily Git Workflow:**
```bash
git status
git add docs/ screenshots/
git commit -m "msg"
git push
```

---

## Project Structure

```
Aurora-AI-Security/
├── README.md
├── .gitignore
├── docs/
│   ├── PROJECT_STATE.md (this file)
│   ├── segment-1/
│   │   ├── Segment1_NetworkPlan_Janzen.pdf
│   │   ├── network-topology.png
│   │   └── Segment 1_ Guidelines.md
│   ├── segment-2/
│   │   ├── Segment 2_ Build & Configure Your Virtualized Network.md
│   │   └── Segment-2_Aurora_Network_Simulation_Design
│   ├── segment-3/
│   └── segment-4/
├── configs/
│   ├── pfsense/
│   ├── alpine/
│   ├── wazuh/
│   └── scripts/
├── screenshots/
│   └── segment-2/
│       ├── 04_pfsense-dashboard.png
│       ├── 05_pfsense-firewall-rules-lan.png
│       ├── 06_pfsense-firewall-rules-vlan40.png
│       ├── 07_ping-win11-to-wazuh.png
│       └── 08_ping-wazuh-to-win11.png
└── portfolio/
    └── cv/
```

---

## Career Context

**Target Roles:** SOC Analyst, Security Operations, Network Security Engineer, GRC Analyst  
**Target Market:** Germany (remote-friendly)  
**Key Differentiators:**
- GDPR/NIS2/IT-SiG compliance knowledge
- Hands-on SIEM experience (Wazuh)
- Hands-on network segmentation (pfSense, VLAN routing)
- Portfolio project on GitHub
- CompTIA Network+ in progress

---

*End of Project State Document*
