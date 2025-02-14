# **Lab 6: Configuring NAT and Internet Access on Mikrotik Routers**  

## **Objective**  
- Understand **Network Address Translation (NAT)** and its importance.  
- Configure **Source NAT (Masquerading)** on a **Mikrotik Router** to enable internet access.  
- Set up **DNS** and verify internet connectivity.  
- Configure **Firewall rules** to enhance security.  

---

## **1. Overview of NAT (Network Address Translation)**  

### **What is NAT?**  
Network Address Translation (NAT) allows multiple devices in a private network to access the **internet using a single public IP address**. It acts as a bridge between **private** and **public networks**.  

### **Why is NAT Needed?**  
- **Preserves IPv4 addresses** by allowing multiple devices to share one public IP.  
- **Hides private network details**, enhancing security.  
- **Allows internet access** for devices with private IP addresses.  

### **Types of NAT:**  
| Type | Description | Common Use |  
|------|-------------|------------|  
| Source NAT (SNAT) | Translates private IP to public IP | Internet access for private networks |  
| Destination NAT (DNAT) | Maps public IP to a private IP | Hosting servers inside private networks |  

---

## **2. Lab Setup & Required Equipment**  
### **Equipment:**  
- **One Mikrotik hAP ac lite Router**  
- **Three PCs** (simulating LAN users)  
- **An ISP Simulator or Internet Connection** (using GNS3 Cloud or real internet connection)  
- **Ethernet cables**  

### **Network Topology:**  

| Device | Interface | IP Address | Subnet Mask |  
|--------|----------|------------|-------------|  
| **Router (Mikrotik hAP ac lite)** | ether1 (WAN) | **DHCP (from ISP)** | Assigned dynamically |  
|  | ether2 (LAN) | 192.168.1.1 | 255.255.255.0 |  
| **PC1** | Connected to LAN | 192.168.1.10 | 255.255.255.0 |  
| **PC2** | Connected to LAN | 192.168.1.20 | 255.255.255.0 |  
| **PC3** | Connected to LAN | 192.168.1.30 | 255.255.255.0 |  

---

## **3. Step-by-Step Configuration**  

### **Step 1: Configure WAN Interface (Ether1) for Internet Access**  

1. **Open WinBox** and connect to the **Mikrotik Router**.  
2. Go to **IP → DHCP Client**.  
3. Click **Add (+)** and configure:  
   - **Interface:** ether1  
   - **Use Peer DNS:** Checked  
   - **Use Peer NTP:** Checked  
   - **Add Default Route:** Checked  
4. Click **Apply → OK**.  

📌 *This allows the router to obtain an IP address from the ISP dynamically.*  

### **Step 2: Configure LAN Interface (Ether2) with Static IP**  

1. Go to **IP → Addresses**.  
2. Click **Add (+)** and set:  
   - **Address:** 192.168.1.1/24  
   - **Interface:** ether2  
3. Click **Apply → OK**.  

📌 *This sets up a private network where all LAN devices will be connected.*  

---

### **Step 3: Configure DHCP Server for LAN**  

To allow automatic IP allocation to LAN devices:  

1. Go to **IP → DHCP Server**.  
2. Click **DHCP Setup**.  
3. Select **ether2** (LAN) and click **Next**.  
4. Set the following:  
   - **Network Address:** 192.168.1.0/24  
   - **Gateway:** 192.168.1.1  
   - **DNS Server:** 8.8.8.8 (Google DNS)  
   - **Lease Time:** 10 minutes (or default)  
5. Click **Next** until the setup is complete.  

📌 *Now, PCs connected to the LAN will automatically receive an IP address.*  

---

### **Step 4: Configure Source NAT (Masquerading) for Internet Access**  

1. Go to **IP → Firewall**.  
2. Open the **NAT** tab.  
3. Click **Add (+)** and configure:  
   - **Chain:** srcnat  
   - **Out Interface:** ether1 (WAN)  
   - **Action:** masquerade  
4. Click **Apply → OK**.  

📌 *This allows all LAN devices to use the router's public IP for internet access.*  

---

### **Step 5: Configure DNS to Enable Name Resolution**  

1. Go to **IP → DNS**.  
2. Set **Servers** to:  
   - **8.8.8.8 (Google DNS)**  
   - **1.1.1.1 (Cloudflare DNS)**  
3. Check **Allow Remote Requests**.  
4. Click **Apply → OK**.  

📌 *This ensures that LAN devices can resolve domain names to IP addresses.*  

---

### **Step 6: Testing Internet Connectivity**  

#### **6.1 Test from the Router**  
1. Open **New Terminal**.  
2. Type the following command:  
   ```
   ping 8.8.8.8
   ```  
   📌 *A successful response confirms internet access.*  

#### **6.2 Test from a LAN PC**  
1. Open **Command Prompt** on PC1.  
2. Run:  
   ```
   ipconfig /all
   ```  
   - Check if the PC has an IP address from **192.168.1.0/24**.  
3. Run:  
   ```
   ping 8.8.8.8
   ```  
   - If successful, the NAT configuration is correct.  
4. Run:  
   ```
   ping www.google.com
   ```  
   - If successful, **DNS resolution is working**.  

---

## **4. Adding Firewall Rules for Security**  

To **enhance security**, restrict external access to the router:  

1. Go to **IP → Firewall**.  
2. Open the **Filter Rules** tab.  
3. Click **Add (+)** and set:  
   - **Chain:** input  
   - **In Interface:** ether1 (WAN)  
   - **Protocol:** TCP  
   - **Dst Port:** 22 (SSH) / 23 (Telnet) / 8291 (WinBox)  
   - **Action:** drop  
4. Click **Apply → OK**.  

📌 *This prevents external users from accessing the router's management interfaces.*  

---

## **5. Conclusion & Next Steps**  
### **What We Achieved:**  
✅ Configured **NAT (Masquerading)** to enable internet access.  
✅ Set up a **DHCP Server** for automatic LAN IP allocation.  
✅ Configured **DNS** for name resolution.  
✅ Verified **internet connectivity** using **ping tests**.  
✅ Implemented **firewall rules** for security.  
