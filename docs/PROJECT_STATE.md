# PROJECT STATE - Aurora AI Security

**Last Updated:** April 9, 2026  
**Student:** Bernd Janzen  
**Program:** Masterschool Cybersecurity Bootcamp  
**Timeline:** February - May 2026 (~12 weeks total)

---

## ⚠️ CRITICAL: EXISTING INFRASTRUCTURE

**DO NOT suggest starting from scratch!**
**DO NOT suggest VirtualBox/VMware!**
**DO NOT suggest downloading new VMs!**

**CURRENT STATE (April 10):**
- ✅ 8 VMs deployed and renamed in UTM with VLAN prefix for clarity
- ✅ pfSense_Aurora: 3 NICs — em0=WAN, em1=VLAN30 (172.16.30.1), em2=VLAN40 (172.16.40.1)
- ✅ Wazuh SIEM at 172.16.30.10 (VLAN30 — IT & Infrastructure)
- ✅ Kali at 192.168.64.x (WAN/external — attacker, no agent)
- ✅ Win11 + Switch-1 moved to UTM "Network 0" bridge (VLAN40)
- 🔜 Switch-2-Alpine: needs IP reconfigured to 172.16.30.11
- 🔜 Switch-1-Alpine: needs IP reconfigured to 172.16.40.11
- 🔜 Windows 11: needs IP reconfigured to 172.16.40.100

**ARCHITECTURE DECISION (April 10):**
Upgraded from flat 172.16.1.x to proper separate VLAN interfaces via UTM bridges.
- UTM "Default (private)" bridge → VLAN30 (IT & Infrastructure) → pfSense em1
- UTM "Network 0" bridge → VLAN40 (Consulting) → pfSense em2
- Traffic between VLANs must route through pfSense — real inter-VLAN enforcement.
- Kali on WAN (external attacker) — NOT VLAN51 (Dirty Lab is internal, not simulated here).

**COMPLETED April 10:**
- ✅ pfSense OPT1 enabled, renamed to VLAN40CONSULTING
- ✅ Pass rules added for LAN (VLAN30) and VLAN40CONSULTING
- ✅ Win11 IP set to 172.16.40.100/24 gateway 172.16.40.1
- ✅ Inter-VLAN routing confirmed: Win11 (VLAN40) ↔ Wazuh (VLAN30) — 0% packet loss
- ✅ pfSense web UI accessible from both VLANs
- ✅ Screenshots 04–08 taken

**REMAINING FOR SEGMENT 2:**
1. Reconfigure Switch-2 IP to 172.16.30.11 (Alpine setup-interfaces)
2. Reconfigure Switch-1 IP to 172.16.40.11 (Alpine setup-interfaces)
3. Replace allow-all rules with proper zone-policy firewall rules
4. Configure pfSense syslog → Wazuh 172.16.30.10 UDP 514 (extension credit)
5. Verify Wazuh agent on Win11 still active
6. Update network diagram
7. Compile PDF: Segment2_VirtualBuild_Janzen.pdf


**Summary from latest installation with Gemini:**
Project Planning: Segment 2 - Network Simulation & SIEM Integration

Status: In Progress
Target Environment: MacBook Air M3 (16GB RAM) | UTM

1. Current State of Infrastructure (As of April 2026)

The foundation of the Virtual SOC (vSOC) is established. The transition from monolithic VMs to a segmented, lightweight architecture is underway.

Active VM Inventory

Node Name

OS / Version

Current State

Role

Wazuh-Manager

Ubuntu 22.04 ARM64

Stable

SIEM / Dashboard / Alerting Engine

pfSense-Aurora

FreeBSD (pfSense)

Running

Firewall / Inter-VLAN Router / DHCP

Switch-1-Alpine

Alpine 3.23.3

Configured

Virtual L2 Switch (Net-1)

Switch-2-Alpine

Alpine 3.23.3

Configured

Virtual L2 Switch (Net-2)

Kali-Linux

Kali ARM64

Ready

Offensive Testing / Security Audit

Windows 11 Arm

Win 11 Pro

Standby

Primary Endpoint Target

Key Achievements (Segment 2)

M3 Optimization: Migrated switching logic to Alpine Linux to keep memory overhead under 256MB per node.

Infrastructure Recovery: Resolved UEFI/Serial Console boot issues on Apple Silicon.

Credential Security: Root access restored and SSH-key authentication implemented via GitHub for administrative nodes.

2. Planned Remaining Tasks for Segment 2

The remaining work focuses on the "Network Simulation" aspect—moving from isolated VMs to a cohesive, monitored traffic flow.

Phase A: Network Hardening & Routing

VLAN Implementation: Configure pfSense to recognize the Alpine nodes as specific VLAN gateways.

Stateless vs. Stateful Inspection: Define rules on pfSense to allow Wazuh agents to "phone home" to the Management Zone while blocking unauthorized traffic between Segment 1 and Segment 2.

Lightweight Monitoring: Install tcpdump on the Alpine switches to verify traffic headers without installing heavy graphical tools.

Phase B: SIEM Agent Deployment

Alpine Wazuh Agent: Deploy the Wazuh agent on the virtual switches to monitor system integrity and login attempts.

Windows Endpoint Integration: Install the Wazuh MSI agent on the Windows 11 VM and verify the heartbeat in the Wazuh Dashboard.

Log Aggregation: Ensure pfSense syslog data is being forwarded to the Wazuh Manager for centralized viewing.

3. Immediate "Claude Code" Action Items

Use these tasks to pick up the work immediately:

[ ] Task 1: Verify IP connectivity between Switch-2-Alpine and Wazuh-Manager via the pfSense gateway.

[ ] Task 2: Update the apk repositories on Alpine switches and install bash and curl for agent installation scripts.

[ ] Task 3: Document the IP addressing schema for the Portfolio to ensure Segment 3 (Exploitation & Detection) has a clear map.

4. Known Technical Constraints (M3 Specific)

Disk Space: 2.1GB limit on Alpine nodes. Monitor with df -h.

Display: Stick to Serial Console [ >_ ] for Alpine to avoid virtio-gpu driver overhead.

Virtualization: Ensure Hypervisor framework is enabled in UTM for native M3 speed.

End of Planning Document

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
- UTM Hypervisor (QEMU)
- Network: 192.168.64.0/24 (UTM Shared Network/NAT mode)

**Virtual Machines (8 total):**

| VM Name | OS | Architecture | Purpose | IP | Status |
|---------|----|--------------|---------|----|--------|
| UTM VM Name | OS | Arch | VLAN | Role | IP | Status |
|---|---|---|---|---|---|---|
| pfSense_Aurora | pfSense 2.7.2 | AMD64 | WAN+30+40 | Router/Firewall/DHCP | em0:192.168.64.11 / em1:172.16.30.1 / em2:172.16.40.1 | ✅ Running |
| VLAN30-Wazuh-Server-Ubuntu 22.04 AMD | Ubuntu 22.04 | AMD64 | VLAN30 | SIEM | 172.16.30.10 | ✅ Operational |
| VLAN30-Switch-2-Alpine | Alpine 3.23.3 | ARM64 | VLAN30 | Virtual L2 Switch | 172.16.30.11 🔜 | Needs IP reconfigure |
| VLAN40-Win11-Target | Windows 11 Pro | ARM64 | VLAN40 | Target endpoint | 172.16.40.100 🔜 | Needs IP reconfigure |
| VLAN40-Switch-1-Alpine | Alpine 3.23.3 | ARM64 | VLAN40 | Virtual L2 Switch | 172.16.40.11 🔜 | Needs IP reconfigure |
| WAN-Kali Linux 2024-Attacker | Kali Linux | ARM64 | WAN | External attacker | 192.168.64.x (DHCP) | ✅ Ready |
| VLAN30-AdminWS-Ubuntu 25.10 ARM | Ubuntu 25.10 | ARM64 | N/A | Optional — Apple Virt only | TBD | Not in VLAN design |
| BACKUP-Ubuntu 22.04 AMD clone | Ubuntu 22.04 | AMD64 | N/A | Backup/Testing | N/A | Backup only |

---

## Segment Status

### ✅ Segment 1: Network Planning (COMPLETE)
- **Duration:** 2 weeks  
- **Deliverable:** 10-page network design PDF  
- **Key Components:**
  - VLAN design (100-500 series)
  - IP addressing scheme
  - NIST CSF framework selection & justification
  - Compliance mapping (GDPR, NIS2, IT-SiG 2.0, FADP, CRA)
  - Risk assessment matrix
- **Status:** Submitted and approved

### 🔄 Segment 2: Virtual Infrastructure Build (IN PROGRESS)
- **Duration:** 2 weeks  
- **Deadline:** Friday, April 11, 2026  
- **Completed:**
  - ✅ 8 VMs deployed in UTM
  - ✅ Wazuh SIEM operational (Manager + Indexer + Dashboard + Filebeat)
  - ✅ All VMs migrated to flat 172.16.1.0/24 internal network (April 9)
  - ✅ Static IPs assigned and confirmed reachable from pfSense
  - ✅ Wazuh agent deployed to Windows 11 (Win11-Aurora — Active)
  - ✅ Alpine agent issue documented (ARM64 — no Wazuh package; syslog mitigation planned)
  - ✅ Kali agent: intentionally skipped (attacker node — no monitoring needed)
  - ✅ Simulation design document created with full architecture rationale
  - ✅ Git repository initialized and connected to GitHub

- **Remaining (tomorrow April 10):**
  - 🔜 Configure pfSense firewall rules (zone policy: MGMT/OPS/ATTACKER)
  - 🔜 Configure pfSense syslog → Wazuh 172.16.1.10 UDP 514 (extension credit)
  - 🔜 Run connectivity tests + take screenshots (ping/traceroute)
  - 🔜 Update network diagram
  - 🔜 Compile PDF: Segment2_VirtualBuild_Janzen.pdf

### 📅 Segment 3: Security & SOC Planning (UPCOMING)
- **Duration:** 2 weeks (starts April 14)  
- **Focus:**
  - Threat modeling (STRIDE methodology)
  - Firewall rules & ACL design
  - SOC workflow definition
  - Incident response playbook
  - Monitoring dashboard design
- **Deliverable:** 14-page security planning PDF

### 📅 Segment 4: Implementation & Validation (FINAL)
- **Duration:** 2 weeks (starts April 28)  
- **Focus:**
  - Deploy security controls in pfSense
  - Configure SIEM alerts & dashboards
  - Vulnerability scanning (OpenVAS/Nessus)
  - Penetration testing from Kali
  - SOC workflow simulation
- **Deliverable:** Final validation PDF

---

## Technical Decisions & Learnings

**Key Issues Resolved:**
1. **Ubuntu 24.04 AMD64 incompatible** with Wazuh under Rosetta 2 (Java JVM segfaults)
   - **Solution:** Downgraded to Ubuntu 22.04 AMD64 ✅

2. **UTM Bridged Mode caused network failures**
   - **Solution:** Switched to Shared Network/NAT mode ✅

3. **Wazuh Dashboard couldn't connect to Indexer**
   - **Root cause:** HTTP vs HTTPS mismatch in /etc/wazuh-dashboard/opensearch_dashboards.yml
   - **Solution:** Changed to `https://192.168.64.7:9200` ✅

4. **Wazuh API timeout errors**
   - **Solution:** Increased timeout from 20,000ms to 60,000ms in dashboard config ✅

**Architecture Notes:**
- ARM vs AMD64 matters: Windows/Kali/Ubuntu 25.10 are ARM native; pfSense/Alpine/Wazuh are AMD64 emulated
- Rosetta 2 emulation has limitations (Java compatibility issues)
- UTM Shared Network provides reliable NAT with DHCP
- UTM does NOT support 802.1Q VLAN tagging → using flat 172.16.1.0/24 with pfSense firewall rules for logical zone separation
- Wazuh Alpine packages are x86_64 only — no ARM64 build → syslog forwarding from pfSense is the mitigation

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

**Dashboard Access:** https://172.16.1.10 (internal) / https://192.168.64.7 (Shared NAT fallback)

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
user.email=manju.janzen@googlemail.com (GitHub account)
```

**Development Environment:**
- **IDE:** VS Code
- **AI Tools:** Claude Code (terminal), GitHub Copilot (inline), Claude Projects (strategy)
- **Terminal:** Integrated in VS Code

**Daily Git Workflow:**
```bash
git status              # Check changes
git add .               # Stage all
git commit -m "msg"     # Commit
git push                # Push to GitHub
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
│   │   └── Segment1_Guidelines.md
│   ├── segment-2/
│   │   ├── Segment2_Guidelines.md
│   │   └── [work in progress]
│   ├── segment-3/
│   │   └── Segment3_Guidelines.md
│   └── segment-4/
│       └── Segment4_Guidelines.md
├── configs/
│   ├── pfsense/
│   ├── alpine/
│   ├── wazuh/
│   └── scripts/
├── screenshots/
│   ├── segment-2/
│   ├── segment-3/
│   └── segment-4/
└── portfolio/
    └── cv/
```

---

## Claude Project Setup

**Project Name:** Aurora AI Security  
**Coverage:** All segments (1-4)

**Uploaded Reference Documents:**
- Segment 1 Network Plan PDF
- Network Topology Diagram
- Segment 2, 3, 4 Guidelines (markdown + PDF)
- This PROJECT_STATE.md

---

## Current Priorities (This Week)

**Wednesday, April 9 — DONE:**
- ✅ Migrated all VMs to 172.16.1.0/24 internal network
- ✅ Windows 11 Wazuh agent confirmed active
- ✅ Architecture decision documented (flat + pfSense rules)

**Thursday, April 10:**
1. Configure pfSense firewall rules (zone policy)
2. Configure pfSense syslog → Wazuh (UDP 514)
3. Run connectivity tests + screenshots
4. Update network diagram
5. Draft Segment 2 PDF

**Friday, April 11:**
1. Finalize Segment 2 PDF (Segment2_VirtualBuild_Janzen.pdf)
2. Submit to Masterschool
3. Live session at 11:00
4. Prepare for Segment 3 (starts April 14)

---

## Known Issues / Questions

- **pfSense VLAN configuration:** Scope unclear - may be optional for lab environment
- **Alpine switches:** Usage unclear - may be optional
- **Segment 2 focus:** Agent deployment vs. full network segmentation (tutor meeting confirmed: work independently with Claude assistance)

---

## Career Context

**Target Roles:** SOC Analyst, Security Operations, Network Security Engineer, GRC Analyst  
**Target Market:** Germany (remote-friendly)  
**Key Differentiators:**
- GDPR/NIS2/IT-SiG compliance knowledge
- Hands-on SIEM experience (Wazuh)
- Portfolio project on GitHub
- Based in Germany

**Study Goals:**
- CompTIA Network+ exam preparation (in parallel)
- Building portfolio for job applications

---

## Contact & Resources

**GitHub:** https://github.com/manjou/aurora-ai-security  
**Claude Pro Subscription:** Active (manju.janzen@googlemail.com dev account)  
**NotebookLM:** Used for GRC/compliance research only  

---

**Usage Instructions:**
1. Save this file to `docs/PROJECT_STATE.md`
2. Update after each segment milestone
3. Use to start fresh Claude conversations (paste entire file for instant context)
4. Git commit after updates

---

*End of Project State Document*