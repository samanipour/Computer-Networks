# **Lab 4: Configuring Static Routing on Mikrotik Routers**  
### **Objective:**  
- Understand **Static Routing** and why it is used.  
- Configure **Static Routes** between two different networks using **two Mikrotik Routers**.  
- Verify connectivity between networks using **ping tests**.  

---

## **1. Overview of Static Routing**  
### **What is Static Routing?**  
Static routing is a **manual routing method** where the network administrator defines specific paths for network traffic using **static routes**.  

### **Why Use Static Routing?**  
- **Predictability**: The network administrator controls the route selection.  
- **Security**: No automatic updates, reducing the risk of incorrect routes.  
- **Efficiency**: Ideal for small networks with few route changes.  

---

## **2. Lab Setup & Required Equipment**  
### **Equipment:**  
- **Two Mikrotik hAP ac lite Routers**  
- **Two PCs (one in each network)**  
- **Ethernet cables**  
- **GNS3 (optional, for virtual lab)**  

### **Network Topology:**  

| Network | Subnet | Assigned Device | Router Interface |  
|---------|--------|----------------|------------------|  
| Network 1 | 192.168.1.0/24 | PC1 | ether2 on Router 1 |  
| Network 2 | 192.168.2.0/24 | PC2 | ether2 on Router 2 |  
| WAN (Router-to-Router Link) | 10.0.0.0/30 | Router 1 (10.0.0.1) | ether1 (connected to Router 2) |  
| | | Router 2 (10.0.0.2) | ether1 (connected to Router 1) |  

- **Router 1** will be configured to route traffic to **Network 2** via **Router 2**.  
- **Router 2** will be configured to route traffic to **Network 1** via **Router 1**.  

---

## **3. Step-by-Step Configuration**  
### **Step 1: Configure Basic IP Addressing**  
#### **1.1 Assign IP Addresses to Router 1**  
1. Open **WinBox** and connect to **Router 1**.  
2. Assign an IP address to **ether1 (WAN - Connected to Router 2)**:  
   ```
   /ip address add address=10.0.0.1/30 interface=ether1
   ```
3. Assign an IP address to **ether2 (LAN - Network 1)**:  
   ```
   /ip address add address=192.168.1.1/24 interface=ether2
   ```
4. Enable the interface:  
   ```
   /interface enable ether1
   /interface enable ether2
   ```

#### **1.2 Assign IP Addresses to Router 2**  
1. Open **WinBox** and connect to **Router 2**.  
2. Assign an IP address to **ether1 (WAN - Connected to Router 1)**:  
   ```
   /ip address add address=10.0.0.2/30 interface=ether1
   ```
3. Assign an IP address to **ether2 (LAN - Network 2)**:  
   ```
   /ip address add address=192.168.2.1/24 interface=ether2
   ```
4. Enable the interface:  
   ```
   /interface enable ether1
   /interface enable ether2
   ```

---

### **Step 2: Configure Static Routes**  
#### **2.1 Add a Route on Router 1 to Network 2**  
- Route traffic destined for **192.168.2.0/24** via **Router 2’s WAN IP (10.0.0.2)**:  
   ```
   /ip route add dst-address=192.168.2.0/24 gateway=10.0.0.2
   ```

#### **2.2 Add a Route on Router 2 to Network 1**  
- Route traffic destined for **192.168.1.0/24** via **Router 1’s WAN IP (10.0.0.1)**:  
   ```
   /ip route add dst-address=192.168.1.0/24 gateway=10.0.0.1
   ```

---

### **Step 3: Configure PCs with Static IPs**  
#### **3.1 Assign IP Addresses to PC1 (Network 1)**  
- IP Address: `192.168.1.2`  
- Subnet Mask: `255.255.255.0`  
- Gateway: `192.168.1.1`  

#### **3.2 Assign IP Addresses to PC2 (Network 2)**  
- IP Address: `192.168.2.2`  
- Subnet Mask: `255.255.255.0`  
- Gateway: `192.168.2.1`  

---

### **Step 4: Verify Connectivity**  
#### **4.1 Test Router-to-Router Communication**  
- From **Router 1**, ping **Router 2’s WAN interface**:  
   ```
   /ping 10.0.0.2
   ```
  - If **ping is successful**, Router 1 can reach Router 2.  

#### **4.2 Test PC-to-PC Communication**  
- From **PC1**, ping **PC2 (192.168.2.2)**:  
   ```
   ping 192.168.2.2
   ```
- From **PC2**, ping **PC1 (192.168.1.2)**:  
   ```
   ping 192.168.1.2
   ```
  - If **ping works**, static routing is correctly configured.  

---

### **Step 5: Troubleshooting (If Ping Fails)**  
1. **Check Static Routes:**  
   ```
   /ip route print
   ```
   - Ensure that **both routes are correctly added**.  

2. **Check Firewall Rules:**  
   ```
   /ip firewall filter print
   ```
   - If needed, allow traffic between networks:  
     ```
     /ip firewall filter add chain=forward action=accept src-address=192.168.1.0/24 dst-address=192.168.2.0/24
     /ip firewall filter add chain=forward action=accept src-address=192.168.2.0/24 dst-address=192.168.1.0/24
     ```

3. **Check Interface Status:**  
   ```
   /interface print
   ```
   - Ensure **ether1 and ether2** are enabled.  

---

## **6. Additional Configurations (Optional)**  
### **Set Up a Default Route (If Connecting to the Internet)**  
- If you need **internet access**, set a default route:  
   ```
   /ip route add dst-address=0.0.0.0/0 gateway=<ISP Gateway>
   ```

### **Enable NAT (If Using Private IPs for Internet)**  
   ```
   /ip firewall nat add chain=srcnat out-interface=ether1 action=masquerade
   ```

---

## **7. Conclusion & Next Steps**  
### **What We Achieved:**  
✔ Configured **Static Routing** on two **Mikrotik Routers**.  
✔ Allowed **inter-network communication** between two different networks.  
✔ Verified the configuration using **ping tests**.  
