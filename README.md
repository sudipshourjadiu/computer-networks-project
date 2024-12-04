Here's the revised **Project Report** with the added **Engineering Standards and Mapping** chapter, detailed **Results and Discussion**, and incorporation of the course outcomes provided.

---

# **Title of Your Mini Lab Project**  
**Secure Internet Access Through WireGuard VPN and Simulated Network Setup in Cisco Packet Tracer**

---

### **Submitted By**  

| **Student Name** | **Student ID** |
|-------------------|----------------|
| Student 1         | ID 1           |
| Student 2         | ID 2           |
| Student 3         | ID 3           |
| Student 4         | ID 4           |
| Student 5         | ID 5           |

---

## **MINI LAB PROJECT REPORT**  
This Report is Presented in Partial Fulfillment of the Course:  
**CSE313 & CSE314: Computer Networks (Theory and Lab)**  
**Department of Computer Science and Engineering**  
**Daffodil International University**  
**Dhaka, Bangladesh**

---

## **Chapter 1: Introduction**

### **1.1 Introduction**  
This project focuses on setting up a secure WireGuard VPN to route client traffic through a server while simulating similar behavior using Cisco Packet Tracer. WireGuard provides a lightweight, high-performance VPN solution with encryption, while Packet Tracer allows for simulating NAT and routing in a controlled environment.

### **1.2 Motivation**  
The need for secure, reliable communication in modern networks is growing. WireGuard's simplicity and high performance make it ideal for achieving this. Simulating the setup using Cisco Packet Tracer offers a visual and educational experience in understanding networking principles.

### **1.3 Objectives**  
- Configure a WireGuard VPN on a Linux server to provide secure internet access.  
- Use NAT and routing to emulate VPN-like behavior in Cisco Packet Tracer.  
- Analyze WireGuard’s PostUp and PostDown configurations.  
- Demonstrate practical applications of network protocols and routing concepts.

### **1.4 Feasibility Study**  
WireGuard’s modern VPN capabilities and Packet Tracer's user-friendly simulation features ensure a feasible project. The simplicity of the WireGuard configuration and Packet Tracer’s support for NAT and routing make this project achievable within the given timeframe.

### **1.5 Gap Analysis**  
While existing studies explore VPN solutions, they rarely integrate lightweight modern VPNs like WireGuard with educational simulation tools. This project bridges that gap by providing a practical and educational perspective.

### **1.6 Project Outcome**  
- Functional WireGuard VPN for secure internet access.  
- Simulated NAT and routing in Cisco Packet Tracer.  
- A detailed understanding of PostUp and PostDown commands in WireGuard.  

---

## **Chapter 2: Proposed Methodology/Architecture**

### **2.1 Overview**  
The project consists of two key parts:  
1. **WireGuard VPN Setup**: A Linux cloud server configures WireGuard to act as a secure gateway.  
2. **Packet Tracer Simulation**: A Cisco router mimics the VPN server’s NAT and routing behavior.

### **2.2 Proposed Methodology**  
1. Deploy WireGuard VPN on a Linux server.  
2. Simulate routing and NAT in Packet Tracer to demonstrate VPN-like behavior.  
3. Analyze the performance and functionality of the configurations.  

### **2.3 Design Specification**
The topology includes a router as the VPN server, connected clients, and an external router representing the internet.

**Figure 2.1: Packet Tracer Network Diagram**

---

## **Chapter 3: Implementation and Results**

### **3.1 Implementation**  

#### **3.1.1 WireGuard Setup on the Server**  
Commands used:
1. **Install WireGuard**:
   ```bash
   sudo apt update
   sudo apt install wireguard -y
   ```
2. **Configure the Server**:
   ```bash
   wg genkey | tee server_private.key | wg pubkey > server_public.key
   echo "net.ipv4.ip_forward=1" | sudo tee -a /etc/sysctl.conf
   sudo sysctl -p
   ```
   Configuration file `/etc/wireguard/wg0.conf`:  
   ```ini
   [Interface]
   PrivateKey = <server_private_key>
   Address = 10.0.0.1/24
   ListenPort = 51820
   PostUp = iptables -A FORWARD -i wg0 -j ACCEPT
   PostUp = iptables -A FORWARD -o wg0 -j ACCEPT
   PostUp = iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
   PostDown = iptables -D FORWARD -i wg0 -j ACCEPT
   PostDown = iptables -D FORWARD -o wg0 -j ACCEPT
   PostDown = iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE
   ```
3. **Start WireGuard**:
   ```bash
   sudo wg-quick up wg0
   sudo systemctl enable [email protected]
   ```

#### **3.1.2 Packet Tracer Configuration**
1. **Router 1 (VPN Server):**
   - Configure NAT and routing to mimic VPN server behavior.
   ```plaintext
   interface G0/0
   ip address 192.168.1.1 255.255.255.0
   ip nat inside
   no shutdown

   interface G0/1
   ip address 203.0.113.1 255.255.255.0
   ip nat outside
   no shutdown

   ip nat inside source list 1 interface G0/1 overload
   access-list 1 permit 192.168.0.0 0.0.255.255
   ip route 0.0.0.0 0.0.0.0 203.0.113.2
   ```
2. **Router 2 (Internet):**
   - Assign a gateway IP:  
   ```plaintext
   interface G0/0
   ip address 203.0.113.2 255.255.255.0
   no shutdown
   ```

---

### **3.2 Results and Discussion**  
#### **Results**  
1. WireGuard successfully routed client traffic through the server, ensuring privacy and encryption.  
2. Packet Tracer simulation demonstrated NAT and routing for VPN-like behavior.  

#### **Discussion**  
- **PostUp Commands**:  
  - Forward traffic between WireGuard clients and the internet.  
  - Perform NAT to masquerade client traffic as originating from the server’s public IP.  

- **PostDown Commands**:  
  - Clean up NAT and forwarding rules to prevent unnecessary traffic after the VPN stops.  

---

## **Chapter 4: Engineering Standards and Mapping**

### **4.1 Mapping with Course Outcomes**  

| **CO** | **Mapping to Project**                                                                                  |
|--------|---------------------------------------------------------------------------------------------------------|
| CO1    | Simulated the organization of networks and proper placement of layers in a VPN and NAT setup.           |
| CO2    | Demonstrated routing and transport layer mechanisms using WireGuard and Packet Tracer.                  |
| CO3    | Configured subnets and routing principles to solve connectivity and NAT-related challenges.              |
| CO4    | Evaluated secure communication principles in VPN and wireless simulations using Packet Tracer.           |

---

## **Chapter 5: Conclusion**

### **5.1 Summary**  
This project implemented a WireGuard VPN to ensure secure internet access and simulated its behavior in Cisco Packet Tracer, demonstrating routing and NAT concepts.

### **5.2 Limitations**  
- Packet Tracer cannot emulate WireGuard encryption.  
- Real-world performance and scalability tests were not included.

### **5.3 Future Work**  
- Implement DNS resolution for clients.  
- Explore load balancing and site-to-site VPN extensions.  

---

## References  
1. WireGuard Documentation: [https://www.wireguard.com](https://www.wireguard.com)  
2. Cisco Packet Tracer Guides: Cisco Networking Academy  

---

Let me know if you need further refinements or additional sections!
