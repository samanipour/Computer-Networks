# **Lab 11: Setting Up DHCP and DNS Server on Windows Server**  

## **Objective**  
- Install and configure a **DHCP Server** to dynamically assign IP addresses.  
- Set up a **DNS Server** to resolve domain names.  
- Verify the correct operation of **DHCP and DNS**.  

---

## **1. Overview of DHCP and DNS**  

### **What is DHCP?**  
DHCP (Dynamic Host Configuration Protocol) automatically assigns **IP addresses, subnet masks, gateways, and DNS settings** to client devices on a network. This eliminates the need to manually configure each device.  

✅ **DHCP Benefits:**  
- **Automatic IP assignment** (No manual configuration).  
- **Centralized management** of network IPs.  
- **Efficient IP allocation** and reuse.  

---

### **What is DNS?**  
DNS (Domain Name System) translates domain names (e.g., `example.com`) into **IP addresses** (e.g., `192.168.1.1`).  

✅ **DNS Benefits:**  
- Allows users to access resources using easy-to-remember names.  
- Reduces network complexity by eliminating the need for IP memorization.  

---

## **2. Lab Setup & Requirements**  

### **Required Software & Hardware:**  
- **Windows Server 2019 or 2022** installed.  
- **Static IP Address** configured.  
- **At least one Windows Client Machine** (Windows 10/11) to test DHCP and DNS.  

📌 *Ensure that your server has a static IP, as DHCP servers should not obtain an IP dynamically.*  

---

## **3. Step-by-Step DHCP Server Installation and Configuration**  

### **Step 1: Install DHCP Role**  

1. **Open Server Manager → Manage → Add Roles and Features**.  
2. Select **Role-based or feature-based installation** and click **Next**.  
3. Select the **Windows Server** where DHCP will be installed → Click **Next**.  
4. Under **Server Roles**, check **DHCP Server**.  
5. Click **Add Features**, then **Next → Next → Install**.  
6. After installation, click **Complete DHCP Configuration**.  
7. **Authorize the DHCP Server** (Select **Administrator Credentials** → Click **Commit**).  

---

### **Step 2: Create and Configure a DHCP Scope**  

1. **Open DHCP Manager** (Run → `dhcpmgmt.msc`).  
2. Expand the **server name**, right-click **IPv4**, and select **New Scope**.  
3. Click **Next**, and enter:  
   - **Scope Name:** `OfficeLAN`  
   - **Starting IP:** `192.168.1.50`  
   - **Ending IP:** `192.168.1.200`  
   - **Subnet Mask:** `255.255.255.0`  
   - Click **Next**.  
4. **Exclude IPs for static devices (e.g., servers, printers):**  
   - Exclude **192.168.1.1 to 192.168.1.20** → Click **Next**.  
5. **Set Lease Duration:** (How long an IP remains assigned to a device).  
   - Default: **8 days** → Click **Next**.  
6. **Configure Gateway (Router):**  
   - Enter **192.168.1.1** → Click **Add → Next**.  
7. **Configure DNS Settings:**  
   - **Preferred DNS:** `192.168.1.10` (Server’s IP)  
   - **Alternate DNS:** `8.8.8.8` (Google DNS)  
   - Click **Next**.  
8. Click **Next → Finish**.  

📌 *Now, the DHCP Server is configured and ready to assign IP addresses to clients.*  

---

### **Step 3: Test DHCP Server Functionality**  

1. On a Windows Client Machine, open **Command Prompt** (`cmd.exe`).  
2. Run:  
   ```sh
   ipconfig /release
   ipconfig /renew
   ```  
3. Verify that the client receives an IP from the **192.168.1.50 – 192.168.1.200** range.  
4. Run:  
   ```sh
   ipconfig /all
   ```  
   - Check if the **DHCP Server** is `192.168.1.10`.  
   - Ensure the **Default Gateway and DNS settings** are correctly assigned.  

📌 *If everything is configured correctly, your client should get an IP from the DHCP server.*  

---

## **4. Step-by-Step DNS Server Installation and Configuration**  

### **Step 1: Install DNS Role**  

1. **Open Server Manager → Manage → Add Roles and Features**.  
2. Select **Role-based or feature-based installation** and click **Next**.  
3. Select the **Windows Server** → Click **Next**.  
4. Under **Server Roles**, check **DNS Server**.  
5. Click **Add Features**, then **Next → Next → Install**.  
6. Once the installation is complete, click **Close**.  

---

### **Step 2: Configure Forward Lookup Zone**  

1. **Open DNS Manager** (`dnsmgmt.msc`).  
2. Right-click **Forward Lookup Zones** → Select **New Zone**.  
3. Click **Next**, then select **Primary Zone** → Click **Next**.  
4. Select **Store in Active Directory** (if using AD) or choose **all DNS servers in this domain**.  
5. Enter a **Zone Name** (e.g., `mycompany.local`).  
6. Choose **Allow both nonsecure and secure updates** → Click **Next → Finish**.  

---

### **Step 3: Configure Reverse Lookup Zone (Optional, but recommended)**  

1. Right-click **Reverse Lookup Zones** → Select **New Zone**.  
2. Choose **Primary Zone** → Click **Next**.  
3. Select **Store in Active Directory** (if using AD).  
4. Enter the **network ID** (e.g., `192.168.1`).  
5. Click **Next → Allow both nonsecure and secure updates → Finish**.  

---

### **Step 4: Create a Host (A) Record**  

1. In **DNS Manager**, expand **Forward Lookup Zones** → Select `mycompany.local`.  
2. Right-click **mycompany.local → New Host (A or AAAA)**.  
3. Enter:  
   - **Name:** `server`  
   - **IP Address:** `192.168.1.10`  
   - Check **Create associated PTR record**.  
4. Click **Add Host → OK → Done**.  

---

### **Step 5: Configure Client Machines to Use DNS**  

1. On a client PC, open **Network Settings → Ethernet → IPv4 Properties**.  
2. Set DNS to:  
   - **Preferred DNS:** `192.168.1.10`  
   - **Alternate DNS:** `8.8.8.8`  
3. Test by running:  
   ```sh
   nslookup server.mycompany.local
   ```  
   - It should return `192.168.1.10`.  

📌 *Now, clients can resolve `server.mycompany.local` to `192.168.1.10`.*  

---

## **5. Conclusion & Next Steps**  

### **What We Achieved:**  
✅ Installed and configured **DHCP Server** for dynamic IP assignment.  
✅ Installed and configured **DNS Server** for domain name resolution.  
✅ Verified that clients successfully receive **IP and DNS settings**.  
