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
