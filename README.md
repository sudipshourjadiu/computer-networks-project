# **WireGuard VPN Tunneling Project**  
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

### **Results and Discussion**

#### **Results**  

1. **Data Privacy and Encryption**:  
   When the client sends data to the WireGuard VPN server, the data is encrypted using the client’s private key and the server’s public key. This ensures that the transmitted data remains secure and cannot be intercepted or understood by external parties. The VPN server decrypts the data using its private key, ensuring that only the intended server can access the information.  

   - **Hiding Internet Accessibility**:  
     Since the data is routed through the VPN server, the client’s actual IP address is hidden from external websites or services. All internet traffic appears to originate from the VPN server's public IP. This mechanism anonymizes the client’s activities, enhancing privacy and security.  

2. **Asymmetric Key Authentication**:  
   WireGuard employs an asymmetric encryption system for authentication:
   - The client and server exchange their **public keys** during the initial handshake.
   - The **client's private key** and the **server's public key** are used to encrypt the connection setup.
   - Similarly, the server uses its **private key** and the **client's public key** to decrypt the setup data and authenticate the client.  

   This mutual authentication ensures that only valid clients can establish connections with the VPN server. The process protects against impersonation and unauthorized access.  

3. **Symmetric Encryption for Data Transfer**:  
   After the initial handshake, WireGuard derives a **shared symmetric session key** using the client and server keys. This session key is used to encrypt and decrypt all subsequent data exchanged between the client and the VPN server.  
   - **Reason for Symmetric Encryption**: While asymmetric encryption is secure, it is computationally expensive for encrypting large amounts of traffic. Symmetric encryption, derived from the asymmetric handshake, is faster and efficient for ongoing communication.  

   The use of symmetric encryption ensures minimal latency while maintaining high security for all client-server traffic.  

4. **Packet Tracer Simulation**:  
   In the Cisco Packet Tracer simulation, the routing and NAT configurations successfully demonstrate the flow of client traffic through a centralized router acting as a VPN server. The simulation mimics VPN-like behavior by hiding the client’s internal IP and presenting the router’s public IP to the external network.

---

#### **Discussion**  

- **Encrypted Data Flow**:  
   WireGuard ensures that data is encrypted during transit using robust encryption algorithms. The client device encrypts outgoing packets with the server's public key before transmitting them to the VPN server. Similarly, the server encrypts its responses with the client’s public key, ensuring end-to-end encryption.  

   This encryption mechanism prevents eavesdroppers on the network from reading or tampering with the data. Even if an attacker intercepts the traffic, they cannot decipher its contents without access to the private keys.

- **Mutual Authentication with Asymmetric Keys**:  
   The asymmetric key pair ensures that:
   - The **server can verify the client's authenticity** before granting access.
   - The **client can verify the server’s authenticity** to avoid man-in-the-middle attacks.  

   This eliminates the need for traditional username-password authentication, relying instead on cryptographic identities.

- **Use of Symmetric Keys for Traffic Encryption**:  
   The symmetric session key, derived during the handshake process, is used for encrypting all data after authentication. This hybrid approach—using asymmetric keys for authentication and symmetric keys for encryption—ensures both security and efficiency.  

- **Privacy and IP Anonymization**:  
   By routing all client traffic through the VPN server, the client’s real IP address is hidden. External services see only the VPN server’s public IP, effectively anonymizing the client’s online activities. This feature is particularly useful for protecting sensitive communications or accessing region-specific resources securely.  

- **Simulation Insights**:  
   The Cisco Packet Tracer simulation effectively demonstrates:
   - How NAT translates private IPs to the public IP of the VPN server.
   - The routing of client traffic through a central VPN-like server.  

   Although the simulation cannot emulate encryption, it visualizes the network flow and logical behavior of a VPN setup, providing an educational perspective on secure networking principles.

--- 

This expanded **Results and Discussion** section now provides a detailed explanation of the encryption mechanisms, the role of asymmetric keys in authentication, the use of symmetric encryption for traffic, and the privacy advantages of routing through a VPN.

### **Chapter 4: Engineering Standards and Mapping**

This chapter outlines the alignment of the project with engineering standards, including its societal and environmental impact, ethical aspects, sustainability, and contribution to teamwork. It also maps the project to course and program outcomes.

---

#### **4.1 Impact on Society, Environment, and Sustainability**

##### **4.1.1 Impact on Life**
The project enhances personal privacy and data security by providing a secure internet access mechanism through a WireGuard VPN. This ensures that sensitive information, such as user credentials and private data, remains protected from interception during transmission. Furthermore, it promotes secure access for remote work and educational activities.

##### **4.1.2 Impact on Society & Environment**
The project has a positive societal impact by advocating for modern, efficient, and secure networking solutions. WireGuard’s lightweight nature reduces server resource consumption, contributing to energy efficiency. By simulating the setup in Cisco Packet Tracer, the project demonstrates an educational tool for understanding sustainable networking solutions with minimal physical resource usage.

##### **4.1.3 Ethical Aspects**
The project adheres to ethical networking practices by implementing privacy-focused solutions. WireGuard uses modern encryption protocols, ensuring the confidentiality and integrity of data. The simulation encourages the ethical application of networking knowledge to secure communication without exploiting vulnerabilities or conducting malicious activities.

##### **4.1.4 Sustainability Plan**
WireGuard’s lightweight design and efficient resource usage make it highly sustainable. Its open-source nature allows continuous development and community contributions, ensuring long-term usability. The project also emphasizes education and simulation using Cisco Packet Tracer, reducing the need for physical hardware, which contributes to a more sustainable learning approach.

---

#### **4.2 Project Management and Team Work**

##### **4.2.1 Cost Analysis**
- **Budget Analysis**:
  - Linux cloud server (e.g., AWS EC2 t2.micro): Free-tier eligible or $5/month.
  - Educational licenses for Cisco Packet Tracer: Free for academic use.
  - No additional hardware costs due to virtualization and simulation tools.

- **Alternate Budget**:
  - Replace cloud hosting with local virtual machines (e.g., VirtualBox) for free.
  - Use open-source simulation alternatives like GNS3 for a fully cost-free setup.

##### **4.2.2 Team Collaboration**
The project was divided into the following tasks to ensure efficient teamwork:
- **Team Member 1**: WireGuard VPN configuration and deployment.
- **Team Member 2**: Packet Tracer simulation setup.
- **Team Member 3**: Results validation and testing.
- **Team Member 4**: Documentation and report preparation.
- **Team Member 5**: Performance analysis and discussion.

Weekly meetings ensured progress tracking, and a shared repository (e.g., GitHub) was used for collaboration.

---

#### **4.3 Complex Engineering Problem**

##### **4.3.1 Mapping of Program Outcomes**
The project maps to the following **Program Outcomes (POs)**:

| **PO** | **Justification**                                                                                      |
|--------|--------------------------------------------------------------------------------------------------------|
| PO1    | Demonstrates networking knowledge by implementing VPN tunneling using modern protocols like WireGuard. |
| PO2    | Solves connectivity and routing challenges by configuring NAT and routing in Packet Tracer.            |
| PO3    | Incorporates ethical practices by focusing on secure and sustainable networking.                       |
| PO4    | Analyzes the impact of networking on society and demonstrates energy-efficient solutions.               |

##### **4.3.2 Complex Problem Solving**

The project aligns with complex problem-solving categories as follows:

| **EP** | **Mapping and Justification**                                                                                                                                  |
|--------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| EP1    | **Depth of Knowledge**: Demonstrates advanced knowledge of networking protocols, encryption, and secure tunneling mechanisms.                                  |
| EP2    | **Range of Conflicting Requirements**: Balances the need for performance, security, and sustainability in both real-world and simulated environments.          |
| EP3    | **Depth of Analysis**: Investigates and implements NAT, routing, and secure communication using modern tools and protocols.                                    |
| EP4    | **Familiarity of Issues**: Tackles common networking issues like IP conflicts, routing loops, and packet drops during simulation and testing.                  |
| EP5    | **Applicable Standards**: Implements widely recognized networking protocols like WireGuard and adheres to educational standards using Packet Tracer.           |
| EP6    | **Stakeholder Involvement**: Engages multiple stakeholders, including students and educators, to develop a learning-oriented networking solution.               |
| EP7    | **Interdependence**: Demonstrates teamwork and integration of tools to provide a seamless, interconnected solution for secure communication.                    |

---

#### **4.4 Engineering Activities**

The project also aligns with engineering activities as detailed below:

| **EA** | **Mapping and Justification**                                                                                                   |
|--------|-------------------------------------------------------------------------------------------------------------------------------|
| EA1    | **Range of Resources**: Uses WireGuard VPN, a cloud-based Linux server, and Cisco Packet Tracer to demonstrate practical applications. |
| EA2    | **Level of Interaction**: Simulates real-world scenarios through team collaboration and software-based testing.                 |
| EA3    | **Innovation**: Leverages modern VPN technologies and simulation tools to provide a unique educational experience.             |
| EA4    | **Consequences for Society and Environment**: Promotes privacy, data security, and sustainable learning practices.              |
| EA5    | **Familiarity**: Demonstrates familiarity with contemporary networking tools and security protocols.                           |

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
