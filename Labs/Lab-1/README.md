# **Lab 1: Creating a Basic LAN (Local Area Network)**  
### **Objective:**  
- Understand the concept of a LAN.  
- Configure a basic **wired LAN** using Mikrotik Router and a Switch.  
- Assign IP addresses to devices and test connectivity.  

### **Required Equipment:**  
- **Mikrotik hAP ac lite** Router  
- **PC or Laptop** (2 or more)  
- **GNS3** installed  
- **Ethernet Cables**  
- **Network Switch**  

### **Step 1: Understanding LAN**  
- A **LAN** is a network that connects computers within a **limited area** (e.g., an office).  
- Devices communicate via **IP addresses** assigned within the same subnet.  

### **Step 2: Physical Connections**  
1. **Power on the Mikrotik Router** and connect it to a **network switch**.  
2. Connect **PCs to the switch** using **Ethernet cables**.  
3. Check **LED indicators** to verify link status.  

### **Step 3: Assigning IP Addresses**  
1. Open **WinBox** and connect to the Mikrotik Router.  
2. Navigate to **IP → Addresses** and add an IP:  
   - **192.168.1.1/24** on **ether1** (router interface).  
3. Assign **static IPs** on each **PC**:  
   - **PC1:** 192.168.1.2/24  
   - **PC2:** 192.168.1.3/24  
4. Verify connectivity using `ping` in the command prompt:  
   ```
   ping 192.168.1.3
   ```  

### **Step 4: Testing LAN Connectivity**  
- Use `ping` to check connectivity between **PCs**.  
- If packets are lost, troubleshoot using:  
  - `ipconfig /all` (Windows)  
  - `ifconfig` (Linux/macOS)  

### **Conclusion:**  
- Successfully set up a **LAN**.  
- Verified **connectivity between devices**.  
- Ready for **Lab 2: VLAN Configuration**.  

---

## **Lab 2: Configuring VLANs (Virtual Local Area Networks)**  
### **Objective:**  
- Understand and configure VLANs on a **Mikrotik switch**.  
- Isolate network traffic using VLANs.  

### **Required Equipment:**  
- **Mikrotik hAP ac lite Router**  
- **Managed Switch** (supports VLANs)  
- **PCs (2 or more)**  
- **Ethernet cables**  
- **GNS3 setup**  

### **Step 1: Understanding VLANs**  
- VLANs separate network traffic at **Layer 2 (Data Link Layer)**.  
- Each VLAN acts as a separate **logical network**.  

### **Step 2: Configuring VLANs on the Switch**  
1. Open **WinBox** and go to **Bridge**.  
2. Create **VLAN IDs**:  
   - VLAN **10** (for Sales Department)  
   - VLAN **20** (for IT Department)  
3. Assign ports to VLANs:  
   - **ether2 → VLAN 10**  
   - **ether3 → VLAN 20**  

### **Step 3: Assigning IPs and Testing**  
1. Assign VLAN **10** IPs to **PC1 (192.168.10.2/24)**.  
2. Assign VLAN **20** IPs to **PC2 (192.168.20.2/24)**.  
3. Test VLAN isolation using `ping`.  

### **Conclusion:**  
- Successfully created **VLANs**.  
- Verified network isolation.  
- Ready for **Lab 3: Configuring Inter-VLAN Routing**.  

---