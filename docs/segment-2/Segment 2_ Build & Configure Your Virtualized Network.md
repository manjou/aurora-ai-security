# **Segment 2: Build & Configure Your Virtualized Network**

Note: Read the requirements well as only a miniaturized version of your network is required to complete this part.

### **📘 Learning Objectives**

By the end of this two-week segment, students will be able to:

1. **Deploy a virtualization environment**  
   * (Specific) Install and configure a hypervisor of choice (VirtualBox/VMware Player for Windows/Linux or VMware Fusion/Parallels Desktop for macOS)  
   * (Measurable) Successfully launch ≥3 VMs  
   * (Achievable) Based on provided installation guides  
   * (Relevant) Mirrors common lab setups  
   * (Time-bound) Within first 2 days  
2. **Instantiate core network devices as VMs**  
   * Create VMs for a router (e.g., pfSense), two switches (Linux bridges or switch VMs), and at least two endpoint servers/PCs  
3. **Apply your IP addressing & VLAN schema**  
   * Assign each VM to the correct VLAN/subnet per your Segment 1 design  
4. **Validate inter-VLAN routing & connectivity**  
   * Prove reachability (ping, traceroute) between VLANs and to the Internet VM  
5. **Document your environment**  
   * Produce a VM inventory table, network diagram update, and configuration summary

---

### **🛠 Prerequisites & Tools**

* **Prerequisites**: Completed Segment 1 plan & PDF

* **Virtualization platform (for future segments):**

   VirtualBox or VMware Player (Windows/Linux) – VMware Fusion or Parallels Desktop (macOS) \-Tryhackme VMs- Packet Tracer

* **OS Images & Software**:

  * Router: pfSense (Community Edition)  
  * Server: Ubuntu Server 22.04 LTS or Windows Server Core  
  * Client: Ubuntu Desktop or Windows 10/11  
* **Utilities**:

  * SSH client (PuTTY, Terminal)  
  * Packet capture: Wireshark  
  * Diagramming (for updates): [draw.io](http://draw.io), Lucidchart

---

### **📝 Task List & Estimated Effort**

| \# | Task | Est. Effort |
| ----- | ----- | ----- |
| 1 | **Install Hypervisor & Import ISOs**: Set up VirtualBox/VMware, import pfSense & OS images, create base VM templates. | 4 h |
| 2 | **Create Network VMs**: Instantiate VMs for router, two switch hosts, and two endpoints; attach to proper virtual networks. | 5 h |
| 3 | **Configure VLANs & Interfaces**: On pfSense and Linux switch VMs, define VLAN interfaces matching Segment 1 (e.g., VLAN 10, 20, 30). | 4 h |
| 4 | **Assign IP Addresses**: Configure static IPs and DHCP scopes per VLAN; verify leases on client VMs. | 4 h |
| 5 | **Enable Inter-VLAN Routing & NAT**: On pfSense, set up LAN interfaces, firewall rules, and NAT for Internet VM. | 4 h |
| 6 | **Test Connectivity**: Run ping/traceroute between endpoints, across VLANs, and to an external “Internet” VM; capture traffic in Wireshark. | 4 h |
| 7 | **Document & Screenshot**: Update your logical diagram, build a VM inventory table, and capture key config screenshots; assemble into PDF. | 5 h |

*Total estimated: 26 h over two weeks (\~13 h/week).*

---

### **🔍 Task Explanations & Industry Rationale**

1. **Hypervisor Setup**  
   * *Why it matters:* In real labs, engineers must stand up virtualization consistently; mastering this ensures reproducible test environments.  
2. **VM Deployment**  
   * *Why it matters:* Translating logical designs into VMs builds hands-on skills and parallels cloud-based network functions.  
3. **VLAN & Interface Config**  
   * *Why it matters:* VLAN segmentation is fundamental for traffic separation and security zoning in enterprise networks.  
4. **IP Assignment & DHCP**  
   * *Why it matters:* Proper addressing and automated IP allocation prevent conflicts and ease device provisioning.  
5. **Routing & NAT**  
   * *Why it matters:* Securely routing between segments and enabling external access is core to perimeter defense and network functionality.  
6. **Connectivity Testing**  
   * *Why it matters:* Validating configurations and troubleshooting with packet captures demonstrates operational readiness.  
7. **Documentation**  
   * *Why it matters:* Clear records of environment and configs support audits, hand-offs, and serve as portfolio artifacts.

---

### **✅ Submission Checklist**

* Virtualization platform installed & base templates created  
* Router & switch VMs instantiated with correct network adapters  
* VLAN interfaces configured on pfSense and switch VMs  
* IP addressing & DHCP scopes applied per VLAN  
* Inter-VLAN routing & NAT implemented  
* Connectivity test results (ping/traceroute) with screenshots  
* Updated network diagram & VM inventory table  
* Single PDF compiled, named Segment2\_VirtualBuild\_\[YourName\].pdf

---

### **📏 Grading Rubric**

| Criterion | Excellent (5) | Good (4) | Fair (3) | Poor (1–2) | Weight |
| ----- | ----- | ----- | ----- | ----- | ----- |
| **VM Deployment & Network Mapping** | All VMs correct & linked | Minor adapter issues | Missing VLANs | Misconfigured VMs | 25% |
| **VLAN & IP Implementation** | Accurate \+ efficient | Minor overlaps | Some misassignments | Incorrect addressing | 25% |
| **Routing & Firewall Rules** | Secure \+ functional | Minor rule gaps | Limited testing | Non-functional | 20% |
| **Testing & Verification** | Comprehensive captures | Basic tests passed | Partial tests only | No tests/results | 20% |
| **Documentation & Presentation** | Professional \+ clear | Minor formatting | Noticeable issues | Hard to follow | 10% |

---

### **🚀 Extension Ideas (Advanced)**

* **DHCP Failover**: Configure secondary DHCP server for high availability.  
* **Syslog Centralization**: Deploy a Linux syslog server to collect pfSense logs.  
* **Network Monitoring**: Install and configure Zabbix agent on endpoints to track uptime.  
* **Backup & Restore**: Script automated VM snapshot backups and restoration drill.

