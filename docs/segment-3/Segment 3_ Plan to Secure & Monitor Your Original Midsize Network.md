# **Segment 3: Plan to Secure & Monitor Your Original Midsize Network**

## **📘 Learning Objectives**

By the end of this two-week segment, students will be able to:

1. **Conduct a threat model & risk analysis**

   * Identify ≥5 threat scenarios against your planned network (core routers, VLANs, workstations)  
   * Rate each threat's likelihood & impact using STRIDE or similar  
2. **Select & justify a security framework**

   * Compare NIST CSF, ISO 27001, MITRE ATT\&CK; write a 300-word rationale for your choice  
3. **Design network-level protections**

   * Draft firewall zones, ACLs, VLAN segmentation controls, and device hardening steps  
4. **Plan monitoring, logging & SOC workflows**

   * Determine log sources (routers, switches, servers, endpoints), retention, central collection (syslog/SIEM)  
   * Define alerting criteria, dashboard requirements, and incident escalation procedures  
5. **Outline SOC roles & responsibilities**

   * Describe at least three SOC functions (e.g., alert triage, incident response, threat hunting) and map them to your network controls  
6. **Produce a security implementation roadmap**

   * Sequence controls, monitoring setup, and SOC onboarding steps in a Gantt-style table

---

## **🛠 Prerequisites & Tools**

**Prerequisites:** Segment 1 PDF (midsize network plan)

**References & Platforms:**

* Firewall design guide (e.g., Cisco ASA or pfSense docs)  
* STRIDE threat modeling worksheet  
* SIEM overview (e.g., Splunk, ELK, Wazuh)  
* SOC process playbook template  
* Documentation: Google Docs/Word → PDF

---

## **📝 Task List & Estimated Effort**

| \# | Task | Est. Effort |
| ----- | ----- | ----- |
| 1 | **Threat Modeling**: Map ≥5 threats to your Segment 1 topology; rate likelihood & impact; create a risk matrix. | 5 h |
| 2 | **Framework Selection**: Compare NIST CSF, ISO 27001, MITRE ATT\&CK; write a 300-word justification. | 4 h |
| 3 | **Firewall & ACL Design**: Draft rules per VLAN/zone; document source/dest, ports, actions in tables. | 5 h |
| 4 | **Device Hardening Plan**: List configurations for routers/switches (disable unused ports, secure SNMP, etc.). | 4 h |
| 5 | **Logging & Monitoring Plan**: Identify log sources, retention policy, collection method; outline key dashboards. | 4 h |
| 6 | **SOC Workflow Definition**: Describe SOC roles (alert triage, IR, threat hunting), escalation paths, and SLAs. | 4 h |
| 7 | **Incident Response Process**: Draft a high-level IR playbook: detection, containment, eradication, recovery steps. | 4 h |
| 8 | **Implementation Roadmap**: Create a timeline table sequencing protections, monitoring build-out, and SOC onboarding. | 4 h |
| 9 | **Assemble PDF**: Combine all deliverables—risk matrix, rationale, rule tables, hardening checklist, monitoring plan, SOC processes, roadmap—into one PDF (≤14 pages). | 3 h |

**Total estimated:** 33 h over two weeks (\~16.5 h/week).

---

## **🔍 Task Explanations & Industry Rationale**

**Threat Modeling & Framework Selection:** Prioritizes controls and aligns them with industry standards.

**Firewall/ACL & Hardening:** Establishes perimeter defenses and reduces device attack surface.

**Logging, Monitoring & SOC Workflows:** Builds visibility and ensures a team can detect, investigate, and respond to incidents—core SOC functions.

**Incident Response Playbook:** Prepares students for real-world security operations and demonstrates an understanding of IR lifecycles.

**Implementation Roadmap:** Shows project management and phased rollout, critical for operational success.

---

## **✅ Submission Checklist**

* Threat model & risk matrix  
* Framework rationale (≥300 words)  
* Firewall rules & ACL tables  
* Device hardening checklist  
* Logging & monitoring plan & dashboard sketches  
* SOC roles & workflow descriptions  
* Incident response playbook outline  
* Implementation roadmap table  
* Single PDF named `Segment3_SecuritySOCPlan_[YourName].pdf`

---

## **📏 Grading Rubric**

| Criterion | Excellent (5) | Good (4) | Fair (3) | Poor (1-2) | Weight |
| ----- | ----- | ----- | ----- | ----- | ----- |
| **Threat Model & Risk Analysis** | Comprehensive threats & realistic ratings | Good coverage | Some threats missing | Superficial | 20% |
| **Framework Justification** | Strong, industry-aligned rationale | Adequate reasoning | Limited justification | Lacks relevance | 15% |
| **Firewall & ACL Design** | Detailed, enforceable rules | Minor gaps | Incomplete rules | Non-functional | 20% |
| **Hardening & Monitoring Plans** | Thorough & practical | Good coverage | Basic plans | Missing critical items | 20% |
| **SOC Workflows & IR Playbook** | Realistic, detailed processes | Adequate workflows | Superficial | Missing key steps | 15% |
| **Roadmap & Documentation** | Professional, clear sequencing | Minor formatting issues | Noticeable gaps | Hard to follow | 10% |

---

## **🚀 Extension Ideas (Advanced)**

* **Deploy IDS/IPS**: Plan inline Suricata on network segments  
* **VPN & MFA Integration**: Design remote access VPN with multi-factor authentication  
* **Threat Hunting Playbook**: Develop queries/rules for proactive threat detection in your SIEM  
* **Automated Response**: Sketch a SOAR workflow for automated containment of high-confidence alerts

---

