# **Lab 9: Configuring L2TP/IPSec VPN on Mikrotik for Secure Remote Access**  

## **Objective**  
- Understand **L2TP/IPSec VPN** and its advantages over PPTP.  
- Configure **L2TP/IPSec VPN Server** on Mikrotik.  
- Configure **Firewall Rules** for VPN security.  
- Set up an **L2TP VPN Client** on a remote PC and test connectivity.  

---

## **1. Overview of L2TP/IPSec VPN**  

### **What is L2TP/IPSec VPN?**  
L2TP (Layer 2 Tunneling Protocol) is a **VPN protocol** that does not provide encryption by itself. When combined with **IPSec (Internet Protocol Security)**, it becomes a **secure and encrypted VPN solution** for remote access.  

### **Why Use L2TP/IPSec Instead of PPTP?**  
| Feature | PPTP | L2TP/IPSec |  
|---------|------|------------|  
| **Security** | Weak | Strong |  
| **Encryption** | MPPE (weak) | AES (strong) |  
| **Firewall Compatibility** | Often blocked | Works well |  
| **Speed** | Fast | Moderate |  

📌 *L2TP/IPSec is a much **more secure** and **widely supported** VPN solution than PPTP.*  

---

## **2. Lab Setup & Required Equipment**  
### **Equipment:**  
- **Mikrotik hAP ac lite Router** (VPN Server)  
- **Remote PC (VPN Client)**  
- **Internet Connection**  

### **Network Topology:**  

| Device | Interface | IP Address | Subnet Mask | Purpose |  
|--------|----------|------------|-------------|----------|  
| **Mikrotik Router** | ether1 (WAN) | **Public IP (from ISP)** | Assigned dynamically | VPN Server |  
|  | ether2 (LAN) | 192.168.1.1 | 255.255.255.0 | Local Network Gateway |  
| **Remote PC (VPN Client)** | Internet | **Public IP** | Assigned dynamically | Connecting to VPN |  

---

## **3. Step-by-Step Configuration**  

### **Step 1: Enable L2TP Server on Mikrotik Router**  

1. Open **WinBox** and connect to the Mikrotik router.  
2. Go to **PPP → Interface**.  
3. Click **Add (+) → L2TP Server**.  
4. Check **Enabled**.  
5. Under **IPSec Secret**, set a strong password (e.g., `vpnsecret`).  
6. Click **Apply → OK**.  

📌 *This enables L2TP VPN with IPSec encryption.*  

---

### **Step 2: Create a VPN User**  

1. Go to **PPP → Secrets**.  
2. Click **Add (+)** and set:  
   - **Name:** vpnuser  
   - **Password:** vpnpassword  
   - **Service:** l2tp  
   - **Local Address:** 192.168.1.1  
   - **Remote Address:** 192.168.1.200  
3. Click **Apply → OK**.  

📌 *This creates a VPN user for remote access.*  

---

### **Step 3: Configure Firewall Rules for L2TP/IPSec**  

To allow L2TP and IPSec traffic:  

1. Go to **IP → Firewall → Filter Rules**.  
2. Click **Add (+)** and set:  
   - **Chain:** input  
   - **Protocol:** UDP  
   - **Dst. Port:** 500  
   - **Action:** accept  
3. Click **Apply → OK**.  

📌 *Allows IPSec key exchange (IKE).*  

4. Click **Add (+)** and set:  
   - **Chain:** input  
   - **Protocol:** UDP  
   - **Dst. Port:** 4500  
   - **Action:** accept  
5. Click **Apply → OK**.  

📌 *Allows IPSec NAT traversal.*  

6. Click **Add (+)** and set:  
   - **Chain:** input  
   - **Protocol:** ESP  
   - **Action:** accept  
7. Click **Apply → OK**.  

📌 *Allows encrypted VPN traffic.*  

8. Click **Add (+)** and set:  
   - **Chain:** input  
   - **Protocol:** TCP  
   - **Dst. Port:** 1701  
   - **Action:** accept  
9. Click **Apply → OK**.  

📌 *Allows L2TP control traffic.*  

---

### **Step 4: Configure IPSec Settings**  

1. Go to **IP → IPsec → Proposals**.  
2. Click **Add (+)** and set:  
   - **Name:** vpn-proposal  
   - **Auth Algorithms:** sha1  
   - **Enc. Algorithms:** aes-256  
   - **PFS Group:** none  
3. Click **Apply → OK**.  

4. Go to **IP → IPsec → Peers**.  
5. Click **Add (+)** and set:  
   - **Address:** `0.0.0.0/0` (Allow all IPs)  
   - **Auth Method:** pre-shared key  
   - **Secret:** vpnsecret  
   - **Exchange Mode:** main  
6. Click **Apply → OK**.  

📌 *This sets up IPSec encryption for secure L2TP VPN connections.*  

---

### **Step 5: Configure L2TP VPN Client on Windows**  

On a **Windows PC**, configure the VPN client:  

1. Open **Settings → Network & Internet → VPN**.  
2. Click **Add a VPN Connection**.  
3. Set:  
   - **VPN Provider:** Windows (Built-in)  
   - **Connection Name:** Mikrotik L2TP  
   - **Server Name or Address:** **Public IP of Mikrotik Router** (e.g., `203.0.113.45`)  
   - **VPN Type:** L2TP/IPSec with pre-shared key  
   - **Pre-shared Key:** vpnsecret  
   - **Username:** vpnuser  
   - **Password:** vpnpassword  
4. Click **Save**.  
5. Click **Connect**.  

📌 *If the connection is successful, the PC is now securely connected to the VPN.*  

---

### **Step 6: Test VPN Connection**  

1. Open **Command Prompt** on the remote PC.  
2. Run the following to check VPN connection:  
   ```
   ipconfig /all
   ```  
   - The PC should receive an IP **192.168.1.200** from the VPN.  
3. Test **pinging the router**:  
   ```
   ping 192.168.1.1
   ```  
   - A successful response confirms VPN connectivity.  
4. Test access to an **internal web server**:  
   ```
   http://192.168.1.100
   ```  
   - If successful, VPN clients can now access internal resources securely.  

---

## **4. Conclusion & Next Steps**  

### **What We Achieved:**  
✅ Configured **L2TP/IPSec VPN Server** on Mikrotik.  
✅ Created **VPN user accounts**.  
✅ Configured **Firewall Rules** to secure the VPN.  
✅ Successfully **connected a remote client** to the VPN.  
✅ Verified **access to internal network resources** over VPN.  
# **Lab 10: Setting Up and Configuring Windows Server**  

## **Objective**  
- Install and configure **Windows Server**.  
- Set up **Active Directory (AD)** and **Domain Controller (DC)**.  
- Create and manage **Group Policies (GPOs)**.  
- Configure **basic roles and features** for network administration.  

---

## **1. Overview of Windows Server**  

**Windows Server** is an operating system designed for **enterprise environments** to manage users, resources, and network security efficiently. It includes roles like **Active Directory, DHCP, DNS, File Server, and Group Policy Management**.  

### **Key Features of Windows Server:**  
✅ **Active Directory (AD)** – Manages users and devices in a domain.  
✅ **Group Policy (GPOs)** – Enforces security policies across the network.  
✅ **Domain Controller (DC)** – Centralized authentication for all users.  
✅ **File and Print Server** – Manages shared files and printers.  

---

## **2. Lab Setup & Required Equipment**  

### **Required Software & Hardware:**  
- **Windows Server 2019 or 2022 ISO** (for installation)  
- **Virtual Machine (VM)** (e.g., VMware, VirtualBox, Hyper-V) or **Physical Server**  
- **Minimum Hardware Requirements:**  
  - **CPU:** 2 GHz, 64-bit  
  - **RAM:** 4GB (8GB recommended)  
  - **Storage:** 80GB or more  
  - **Network Adapter:** Configured for connectivity  

---

## **3. Step-by-Step Installation and Setup**  

### **Step 1: Installing Windows Server**  

1. **Boot from ISO:**  
   - Attach the **Windows Server ISO** to your VM or burn it to a USB for physical installation.  
   - Restart the server and boot from the installation media.  

2. **Select Language and Region:**  
   - Choose **English (United States)** and click **Next**.  
   - Click **Install Now**.  

3. **Select Windows Server Edition:**  
   - Choose **Windows Server 2019 Standard (Desktop Experience)**.  
   - Click **Next**.  

4. **Accept License Agreement:**  
   - Check **I accept the license terms** and click **Next**.  

5. **Select Installation Type:**  
   - Choose **Custom: Install Windows Only (Advanced)**.  

6. **Select Disk Partition:**  
   - Choose the primary disk and click **Next**.  

7. **Wait for Installation to Complete:**  
   - The system will install Windows Server and restart automatically.  

8. **Set Administrator Password:**  
   - After reboot, create a **strong administrator password** and log in.  

---

### **Step 2: Configuring Network and Computer Name**  

1. **Rename the Server:**  
   - Open **Server Manager → Local Server**.  
   - Click on **Computer Name** and change it to something like `WinServer01`.  
   - Restart the server for changes to apply.  

2. **Configure Static IP Address:**  
   - Go to **Control Panel → Network and Internet → Network Connections**.  
   - Right-click **Ethernet Adapter → Properties**.  
   - Select **Internet Protocol Version 4 (TCP/IPv4)** → Properties.  
   - Set the following:  
     - **IP Address:** `192.168.1.10`  
     - **Subnet Mask:** `255.255.255.0`  
     - **Default Gateway:** `192.168.1.1`  
     - **Preferred DNS:** `127.0.0.1` (for future AD setup)  
   - Click **OK** and **Close**.  

---

### **Step 3: Installing Active Directory (AD) and Domain Controller (DC)**  

1. **Open Server Manager → Manage → Add Roles and Features**.  
2. Select **Role-based or feature-based installation** → Click **Next**.  
3. Choose the local server **(WinServer01)** → Click **Next**.  
4. Under **Roles**, check **Active Directory Domain Services (AD DS)**.  
5. Click **Add Features**, then **Next → Next → Install**.  
6. After installation, click **Promote this server to a domain controller**.  
7. Select **Add a new forest**, enter **Domain Name** (e.g., `mycompany.local`), and click **Next**.  
8. Set a **DSRM Password** (for directory recovery) → Click **Next → Next → Install**.  
9. The server will restart and become a **Domain Controller**.  

---

### **Step 4: Creating and Managing User Accounts**  

1. **Open Active Directory Users and Computers**.  
2. Navigate to **mycompany.local → Users**.  
3. Right-click **Users → New → User**.  
4. Enter details:  
   - **First Name:** John  
   - **Last Name:** Doe  
   - **Username:** jdoe  
5. Set a **password** and check **User must change password at next logon**.  
6. Click **Finish**.  

📌 *John Doe can now log in to the domain from any domain-joined PC.*  

---

### **Step 5: Setting Up Group Policy (GPOs) for Security**  

1. **Open Group Policy Management**.  
2. Right-click **mycompany.local → Create a GPO in this domain**.  
3. Name it **Security Policy** → Click **OK**.  
4. Right-click **Security Policy → Edit**.  
5. Navigate to:  
   - **Computer Configuration → Policies → Windows Settings → Security Settings**.  
   - Modify **Password Policy**:  
     - **Minimum password length:** 8  
     - **Enforce password history:** 5 passwords  
     - **Account lockout policy:** Lock after 3 failed attempts  
6. Close Group Policy Editor.  
7. Link GPO to the domain:  
   - Right-click **mycompany.local → Link an Existing GPO → Select Security Policy**.  
   - Click **OK**.  

📌 *Now, all domain users must follow the new security rules.*  

---

### **Step 6: Configuring File Sharing and Permissions**  

1. **Create a Shared Folder:**  
   - Go to **C:\**, create a folder named **CompanyFiles**.  
   - Right-click **CompanyFiles → Properties → Sharing → Advanced Sharing**.  
   - Check **Share this folder** and name it **SharedDocs**.  
   - Click **Permissions**, add **Domain Users**, and set **Read & Write** access.  
   - Click **Apply → OK**.  

2. **Test Network Access:**  
   - On another PC, open **File Explorer** and enter `\\WinServer01\SharedDocs`.  
   - The folder should be accessible to all domain users.  

📌 *This allows users to store and share files securely.*  

---

## **4. Conclusion & Next Steps**  

### **What We Achieved:**  
✅ Installed **Windows Server**.  
✅ Configured **Static IP and Domain Controller (DC)**.  
✅ Created **User Accounts in Active Directory**.  
✅ Applied **Group Policy for security**.  
✅ Set up **File Sharing and Permissions**.  
