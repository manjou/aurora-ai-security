# **Segment 4: Implement & Validate Your Security Plan in the Virtualized Network**

### **📘 Learning Objectives**

By the end of this two-week segment, students will be able to:

1. **Deploy security controls** in the virtual lab  
   * Translate your firewall rules, ACLs, and device-hardening steps into pfSense and switch VM configurations  
2. **Configure monitoring & alerting**  
   * Integrate VMs with your chosen syslog/SIEM, build key dashboards, and tune at least three alert rules  
3. **Establish SOC processes**  
   * Simulate alert triage and incident response tasks using captured logs/alerts from the lab  
4. **Validate defenses**  
   * Perform penetration tests or vulnerability scans (OpenVAS/Nessus) against the lab, document findings, and remediate at least two issues  
5. **Produce a final validation report**  
   * Compile evidence of controls, monitoring, SOC activities, and remediation into a cohesive PDF

---

### **🛠 Prerequisites & Tools**

* **Prerequisites:** Completed Segment 2 lab & Segment 3 security/SOC plans  
* **Lab Platform:** VirtualBox/VMware (Windows/Linux) or Fusion/Parallels (macOS) with your network VMs running  
* **Software & Services:**  
  * pfSense firewall VM  
  * Linux switch VMs or virtual switch integrations  
  * Syslog server or SIEM instance (ELK Stack, Splunk Free)  
  * Vulnerability scanner (OpenVAS/Nessus Essentials)  
  * Attack toolkit (Kali Linux VM or equivalent)  
* **Utilities:**  
  * SSH/Telnet client  
  * Web browser for dashboards  
  * Packet capture (Wireshark)

---

### **📝 Task List & Estimated Effort**

| \# | Task | Est. Effort |
| ----- | ----- | ----- |
| 1 | **Implement Firewall & ACLs**: Configure pfSense rules and switch ACLs exactly as per Segment 3 tables. | 5 h |
| 2 | **Harden Devices**: Apply your hardening checklist to router/switch VMs (disable services, change creds). | 4 h |
| 3 | **Set Up Logging**: Forward logs from pfSense and endpoints to your syslog/SIEM; verify receipt. | 4 h |
| 4 | **Build Dashboards & Alerts**: Create at least two dashboards and three alert rules (e.g., port scan, failed login). | 5 h |
| 5 | **Simulate SOC Workflow**: Generate test alerts (e.g., malformed packets, brute-force attempts), triage them, and document your response steps. | 5 h |
| 6 | **Vulnerability Assessment**: Run a vulnerability scan against the lab, analyze results, remediate two critical/high findings. | 5 h |
| 7 | **Validation Testing**: From your attack VM, attempt lateral movement or known exploits; capture evidence of both success/failure. | 4 h |
| 8 | **Compile Final PDF**: Assemble configurations, logs screenshots, SOC notes, scan reports, and remediation summary into Segment4\_Validation\_\[YourName\].pdf. | 3 h |

*Total estimated: 35 h over two weeks (\~17.5 h/week).*

---

### **🔍 Task Explanations & Industry Rationale**

* **Firewall & ACL Implementation:** Ensures theoretical designs become enforceable controls, mirroring enterprise deployments.  
* **Device Hardening:** Closes real-world attack paths; an essential best practice for any infrastructure.  
* **Logging & SIEM Setup:** Provides visibility and centralized analysis—core to modern SOC operations.  
* **Dashboard & Alert Tuning:** Teaches how to convert raw data into actionable insights and reduce alert fatigue.  
* **SOC Simulation:** Exercises the human processes behind alerts—critical for entry-level SOC analysts and consultants.  
* **Vulnerability Scanning & Remediation:** Demonstrates continuous improvement and defensive validation.  
* **Attack Testing:** Confirms controls hold under adversarial conditions and highlights gaps for remediation.  
* **Reporting:** Produces the polished, portfolio-ready artifact recruiters look for.

---

### **✅ Submission Checklist**

* pfSense firewall & switch ACL configurations implemented  
* Device hardening steps applied (screenshots/config exports)  
* Logs successfully forwarded and visible in SIEM/syslog server  
* Dashboards created and alert rules verified (screenshots)  
* SOC simulation notes and incident triage documentation  
* Vulnerability scan report and remediation evidence for ≥2 issues  
* Attack test evidence (packets, screenshots, success/failure notes)  
* Final validation PDF named Segment4\_Validation\_\[YourName\].pdf

---

### **📏 Grading Rubric**

| Criterion | Excellent (5) | Good (4) | Fair (3) | Poor (1–2) | Weight |
| ----- | ----- | ----- | ----- | ----- | ----- |
| **Control Implementation** | Exact & enforceable | Minor config issues | Some rules missing | Controls non-functional | 25% |
| **Logging & Alerting** | Comprehensive & tuned alerts | Basic dashboards | Limited alerts | No monitoring data | 20% |
| **SOC Workflow Execution** | Realistic & detailed triage | Adequate notes | Superficial | Missing process | 20% |
| **Vulnerability Remediation** | Identified & fixed issues | Fixed only some | Partial remediation | No remediation | 20% |
| **Attack Validation** | Mixed outcomes & clear evidence | Basic tests passed | Limited tests | No tests/results | 10% |
| **Reporting & Presentation** | Professional & cohesive PDF | Minor formatting issues | Noticeable errors | Hard to follow | 5% |

---

### **🚀 Extension Ideas (Advanced)**

* **Automated Playbooks:** Use Ansible or scripts to deploy and enforce your security configurations.  
* **Threat Hunting Queries:** Develop SIEM queries for behavioral anomalies (e.g., lateral movement patterns).  
* **Compliance Checks:** Integrate open-source compliance tools (e.g., Lynis, OpenSCAP) and report on compliance posture.  
* **SOAR Integration:** Sketch a basic SOAR playbook to automate containment of detected threats.

---

# **Submission form**

[Project Submission Form](https://www.notion.so/2419418319f3806984cfcef9cf490f34?pvs=21)

