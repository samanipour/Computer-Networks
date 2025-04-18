# **Lab 8: Configuring VPN on Mikrotik Router for Secure Remote Access**  

## **Objective**  
- Understand the **importance of VPNs** and how they provide **secure remote access**.  
- Configure **PPTP VPN Server** on a **Mikrotik Router**.  
- Configure a **VPN Client** on a remote computer to connect securely.  
- Implement **firewall rules** for VPN security.  

---

## **1. Overview of VPN (Virtual Private Network)**  

### **What is a VPN?**  
A **VPN (Virtual Private Network)** creates a **secure encrypted connection** over the internet, allowing users to access a private network remotely.  

### **Why Use a VPN?**  
- **Secure Remote Access:** Users can securely connect to a **company network** or **home network** from anywhere.  
- **Data Encryption:** Encrypts communication, **preventing data interception** by hackers.  
- **Bypass Restrictions:** Allows users to access **internal services and resources** from external networks.  

### **Types of VPN Supported by Mikrotik:**  
| VPN Type | Encryption | Use Case |  
|----------|------------|----------|  
| **PPTP (Point-to-Point Tunneling Protocol)** | Weak | Simple VPN, easy to set up |  
| **L2TP/IPSec (Layer 2 Tunneling Protocol with IPSec)** | Strong | Secure VPN for remote workers |  
| **OpenVPN** | Strong | Secure & flexible |  
| **WireGuard** | Very Strong | Fast & modern VPN solution |  

📌 *In this lab, we will configure **PPTP VPN** as it is easy to set up and widely supported.*  

---

## **2. Lab Setup & Required Equipment**  
### **Equipment:**  
- **One Mikrotik hAP ac lite Router** (VPN Server)  
- **One Remote PC (VPN Client)**  
- **Internet Connection**  
- **Ethernet cables**  

### **Network Topology:**  

| Device | Interface | IP Address | Subnet Mask | Purpose |  
|--------|----------|------------|-------------|----------|  
| **Mikrotik Router** | ether1 (WAN) | **Dynamic Public IP (from ISP)** | Assigned dynamically | VPN Server |  
|  | ether2 (LAN) | 192.168.1.1 | 255.255.255.0 | Local Network Gateway |  
| **Remote PC (VPN Client)** | Internet | **Public IP** | Assigned dynamically | Connecting to VPN |  

---

## **3. Step-by-Step Configuration**  

### **Step 1: Enable PPTP VPN Server on Mikrotik Router**  

1. Open **WinBox** and connect to the Mikrotik router.  
2. Go to **PPP → PPTP Server**.  
3. Click **Enable**.  
4. Under **Default Profile**, click on **Profiles** and select **default-encryption**.  
5. Click **Apply → OK**.  

📌 *The router is now ready to accept PPTP VPN connections.*  

---

### **Step 2: Create a VPN User**  

1. Go to **PPP → Secrets**.  
2. Click **Add (+)**.  
3. Configure the following:  
   - **Name:** vpnuser  
   - **Password:** vpnpassword  
   - **Service:** pptp  
   - **Local Address:** 192.168.1.1  
   - **Remote Address:** 192.168.1.200  
4. Click **Apply → OK**.  

📌 *This creates a VPN user who will connect to the Mikrotik VPN server remotely.*  

---

### **Step 3: Configure Firewall Rules for VPN**  

To **allow VPN traffic** and **enhance security**:  

1. Go to **IP → Firewall → Filter Rules**.  
2. Click **Add (+)** and set:  
   - **Chain:** input  
   - **Protocol:** TCP  
   - **Dst. Port:** 1723 (PPTP)  
   - **Action:** accept  
3. Click **Apply → OK**.  

📌 *This allows incoming VPN connection requests.*  

4. Click **Add (+)** and set:  
   - **Chain:** input  
   - **Protocol:** GRE  
   - **Action:** accept  
5. Click **Apply → OK**.  

📌 *PPTP requires GRE (Generic Routing Encapsulation) to function.*  

6. Click **Add (+)** and set:  
   - **Chain:** input  
   - **Src. Address:** 192.168.1.200  
   - **Action:** accept  
7. Click **Apply → OK**.  

📌 *This allows VPN clients to communicate with LAN resources.*  

8. Finally, **drop all other inbound traffic from the internet**:  
   - **Chain:** input  
   - **In. Interface:** ether1 (WAN)  
   - **Action:** drop  
   - Click **Apply → OK**.  

📌 *This blocks unauthorized access to the router from the internet.*  

---

### **Step 4: Configure a VPN Client on Windows**  

On a remote **Windows PC**, configure a PPTP VPN client:  

1. Open **Settings → Network & Internet → VPN**.  
2. Click **Add a VPN Connection**.  
3. Set the following:  
   - **VPN Provider:** Windows (Built-in)  
   - **Connection Name:** Mikrotik VPN  
   - **Server Name or Address:** **Public IP of the Mikrotik Router** (e.g., `203.0.113.45`)  
   - **VPN Type:** PPTP  
   - **Username:** vpnuser  
   - **Password:** vpnpassword  
4. Click **Save**.  
5. Click **Connect**.  

📌 *If the connection is successful, the remote PC is now securely connected to the Mikrotik VPN server.*  

---

### **Step 5: Test VPN Connection**  

1. Open **Command Prompt** on the remote PC.  
2. Run the following command to check if the VPN is connected:  
   ```
   ipconfig /all
   ```  
   - The PC should receive an IP address **192.168.1.200** from the VPN.  
3. Test **pinging the router**:  
   ```
   ping 192.168.1.1
   ```  
   - A successful response confirms connectivity.  
4. Test access to **internal services (e.g., a file server or web server)**:  
   ```
   http://192.168.1.100
   ```  
   - If successful, VPN clients can now access internal network resources.  

---

## **4. Conclusion & Next Steps**  

### **What We Achieved:**  
✅ Configured **PPTP VPN Server** on Mikrotik Router.  
✅ Created **VPN user accounts**.  
✅ Configured **Firewall Rules** to secure the VPN.  
✅ Successfully **connected a remote client** to the VPN.  
✅ Verified **access to internal network resources** over VPN.  