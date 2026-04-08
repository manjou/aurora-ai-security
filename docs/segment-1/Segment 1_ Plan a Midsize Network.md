# **Segment 1: Plan a Midsize Network**

### **📘 Learning Objectives**

By the end of this two-week segment, students will be able to:

1. **Define clear network requirements**  
   1. (Specific) Gather functional, performance, and security requirements for a 50–100-user office network  
   2. (Measurable) Document ≥5 distinct requirements  
   3. (Achievable) Based on A+/Net+/Sec+ knowledge  
   4. (Relevant) Mirrors real-world network design intake  
   5. (Time-bound) Within first 3 days  
2. **Design a logical topology diagram** with VLANs, subnets, and core devices  
3. **Develop an IP addressing scheme** that maximizes address space efficiency  
4. **Produce a hardware & software inventory** aligned to requirements  
5. **Perform a high-level risk assessment**, selecting a guiding framework and justifying it

### **🛠 Prerequisites & Tools**

**Knowledge**: CompTIA A+, Security+ (all); Network+ OR AI course

**Tools**:

* Diagramming: [draw.io](http://draw.io), Lucidchart, or similar  
* Documentation: Google Docs / Word → final PDF  
* Virtualization platform (for future segments): VirtualBox or VMware Player (Windows/Linux) – VMware Fusion or Parallels Desktop (macOS)

**Resources**:

* Sample network requirements template  
* Intro to NIST CSF & MITRE ATT\&CK overviews

### **📝 Task List & Estimated Effort**

| \# | Task | Est. Effort |
| ----- | ----- | ----- |
| 1 | **Gather Requirements**: Interview "stakeholders" (via provided prompts) and list functional, performance, and security needs. [\[Link\]](https://www.notion.so/2379418319f380aa98feebf2479d43a6?pvs=21) | 4 h |
| 2 | **Choose & Justify a Framework**: For Example Read brief overviews of NIST CSF vs. MITRE ATT\&CK; select one to guide your design and write a 300-word rationale. | 3 h |
| 3 | **Draft Logical Topology**: For Example Using your requirements, sketch a diagram showing core switches, routers, VLANs, and segments. | 5 h |
| 4 | **Design IP Scheme**: For Example Allocate subnets (e.g., /24, /26) to each VLAN; document address ranges, gateway IPs, and DHCP scopes in a table. | 4 h |
| 5 | **Compile Inventory**: List required hardware (model, spec, qty) and software/services (OS, licensing) mapped to requirements. | 3 h |
| 6 | **Risk Assessment**: Identify top 3 network risks (e.g., misconfigurations, unauthorized access) and map each to a control from your chosen framework. | 4 h |
| 7 | **Assemble PDF**: Combine diagram, tables, rationale, and assessment into a single PDF (≤10 pages). | 3 h |

*Total estimated: 26 h over two weeks (\~13 h/week).*

### **🔍 Task Explanations & Industry Rationale**

**Requirements Gathering**

*Why it matters:* Real network designs begin by understanding business needs. Missing or misconstrued requirements lead to scope creep or insecure designs.

**Framework Selection**

*Why it matters:* Frameworks like NIST CSF provide repeatable, audited approaches. Justifying your choice demonstrates critical decision-making.

**Topology Diagramming**

*Why it matters:* Visual maps are the lingua franca of network teams; clear diagrams speed up troubleshooting and approvals.

**IP Scheme Design**

*Why it matters:* Efficient addressing avoids waste and supports growth. Documenting scopes prevents overlaps in large environments.

**Inventory Compilation**

*Why it matters:* Accurate bills of materials inform budgeting and procurement; missing items delay roll-out.

**Risk Assessment Mapping**

*Why it matters:* Proactively addressing risks demonstrates security-first thinking and aligns with auditor expectations.

**PDF Assembly**

*Why it matters:* A polished deliverable simulates professional proposals and becomes the portfolio artifact recruiters will review.

### **✅ Submission Checklist**

* Network requirements document (≥5 items)  
* Framework rationale (≥300 words)  
* Logical topology diagram (exported PNG/PDF)  
* IP addressing table (clear columns: VLAN, subnet, range)  
* Hardware & software inventory list  
* Risk assessment mapping table  
* Single PDF compiled, named Segment1\_NetworkPlan\_\[YourName\].pdf

### **📏 Grading Rubric**

| Criterion | Excellent (5) | Good (4) | Fair (3) | Poor (1–2) | Weight |
| ----- | ----- | ----- | ----- | ----- | ----- |
| **Topology Clarity** | Crystal-clear, labeled | Mostly clear | Some labels missing | Confusing layout | 25% |
| **Framework Justification** | Strong, industry-aligned | Good rationale | Limited justification | Lacks relevance | 20% |
| **Addressing Accuracy & Efficiency** | Optimal, documented | Minor overlaps | Some gaps | Incorrect ranges | 20% |
| **Inventory Completeness** | All items mapped clearly | Minor omissions | Several items missing | Incomplete | 15% |
| **Risk Assessment & Control Mapping** | Insightful, well-mapped | Reasonable mapping | Superficial | Missing controls | 15% |
| **Documentation Quality & Formatting** | Professional, error-free | Minor typos/layout | Noticeable issues | Poor readability | 5% |

### **🚀 Extension Ideas (Advanced)**

* **VLAN Segmentation Deep-Dive**: Propose guest vs. corporate VLAN ACLs.  
* **High-Availability Design**: Add redundant links or VRRP configurations.  
* **Cost Analysis**: Research device MSRP and estimate project budget.  
* **Firewall Rule Set**: Draft basic ACL/firewall rules for inter-VLAN traffic.  
* **Domain Environment Planning**: Include a plan for a domain environment in your network.

