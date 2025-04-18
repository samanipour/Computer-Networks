# **Lab 7: Configuring Port Forwarding and Accessing Internal Services Remotely**  

## **Objective**  
- Understand **Port Forwarding (Destination NAT - DNAT)** and its use cases.  
- Configure **Port Forwarding on Mikrotik Router** to allow external access to internal servers.  
- Secure the port forwarding setup with **Firewall Rules**.  
- Test access to an internal service from an external network.  

---

## **1. Overview of Port Forwarding (DNAT)**  

### **What is Port Forwarding?**  
Port forwarding, also called **Destination NAT (DNAT)**, allows external users to access specific services hosted within a **private network** by mapping a **public IP and port** to a **private IP and port**.  

### **Why is Port Forwarding Needed?**  
- Allows remote access to **web servers, game servers, SSH, RDP, or other hosted services** inside a private network.  
- Makes a private service **accessible from the internet** while keeping the rest of the network secure.  
- Useful for **remote administration** of network devices.  

---

## **2. Lab Setup & Required Equipment**  
### **Equipment:**  
- **One Mikrotik hAP ac lite Router**  
- **One Internal Web Server (e.g., Apache, Nginx, or HTTP Server on a PC)**  
- **A Remote PC (External User) to test access from the internet**  
- **Ethernet cables**  

### **Network Topology:**  

| Device | Interface | IP Address | Subnet Mask | Purpose |  
|--------|----------|------------|-------------|----------|  
| **Mikrotik Router** | ether1 (WAN) | **Dynamic Public IP (from ISP)** | Assigned dynamically | Connected to ISP |  
|  | ether2 (LAN) | 192.168.1.1 | 255.255.255.0 | LAN Gateway |  
| **Web Server (Internal Host)** | Connected to LAN | 192.168.1.100 | 255.255.255.0 | Hosting Web Service |  
| **External PC (Internet User)** | Internet | **Public IP** | Assigned dynamically | Simulating a remote user |  

---

## **3. Step-by-Step Configuration**  

### **Step 1: Configure the Internal Web Server**  

1. Install **Apache/Nginx** on a LAN computer (e.g., PC with IP **192.168.1.100**).  
2. Start the web server service.  
   - If using **Apache** on Windows:  
     - Download **XAMPP** and start **Apache**.  
   - If using **Nginx** on Linux:  
     ```
     sudo apt install nginx -y
     sudo systemctl start nginx
     ```  
3. Open a web browser and visit `http://192.168.1.100`.  
4. If the web page loads successfully, the web server is running.  

---

### **Step 2: Configure Port Forwarding (DNAT) on Mikrotik Router**  

To allow external users to access the internal web server:  

1. Open **WinBox** and connect to the Mikrotik router.  
2. Go to **IP → Firewall**.  
3. Open the **NAT** tab.  
4. Click **Add (+)** and set the following:  
   - **Chain:** dstnat  
   - **Protocol:** TCP  
   - **Dst. Port:** **80** (for HTTP)  
   - **In. Interface:** ether1 (WAN)  
   - **Action:** dst-nat  
   - **To Address:** 192.168.1.100  
   - **To Ports:** 80  
5. Click **Apply → OK**.  

📌 *This rule ensures that any request coming to the router's public IP on port 80 is forwarded to the internal web server.*  

---

### **Step 3: Allow Incoming Connections via Firewall Rules**  

By default, Mikrotik blocks external access. To allow HTTP traffic:  

1. Go to **IP → Firewall**.  
2. Open the **Filter Rules** tab.  
3. Click **Add (+)** and set the following:  
   - **Chain:** forward  
   - **Protocol:** TCP  
   - **Dst. Port:** 80  
   - **In. Interface:** ether1 (WAN)  
   - **Dst. Address:** 192.168.1.100  
   - **Action:** accept  
4. Click **Apply → OK**.  

📌 *Now, external users can access the internal web server.*  

---

### **Step 4: Test Remote Access from an External Network**  

1. Find the **Public IP Address** of the router:  
   - Go to **IP → Addresses** and check the IP on **ether1**.  
   - Or, visit `https://www.whatismyip.com/` from a device inside the network.  
2. From an **external PC (outside the LAN)**, open a web browser and enter:  
   ```
   http://<Public-IP>
   ```  
   Example:  
   ```
   http://203.0.113.45
   ```  
3. If the web server page loads, the port forwarding is working correctly.  

📌 *Now, anyone on the internet can access the internal web server using the router's public IP address.*  

---

### **Step 5: Secure the Port Forwarding Setup**  

To prevent unauthorized access, apply security rules:  

#### **5.1 Restrict Access to Specific IPs**  
If only certain external IPs should access the web server:  

1. Go to **IP → Firewall → Filter Rules**.  
2. Click **Add (+)** and set:  
   - **Chain:** forward  
   - **Protocol:** TCP  
   - **Dst. Port:** 80  
   - **In. Interface:** ether1 (WAN)  
   - **Src. Address:** (Allowed External IP)  
   - **Action:** accept  
3. Click **Apply → OK**.  

📌 *This ensures only authorized external IPs can access the server.*  

#### **5.2 Limit Connections per IP**  
To prevent DDoS attacks:  

1. Go to **IP → Firewall → Filter Rules**.  
2. Click **Add (+)** and set:  
   - **Chain:** forward  
   - **Protocol:** TCP  
   - **Dst. Port:** 80  
   - **Connection Limit:** 10  
   - **Action:** drop  
3. Click **Apply → OK**.  

📌 *This limits excessive connections from a single IP to prevent abuse.*  

---

## **4. Conclusion & Next Steps**  

### **What We Achieved:**  
✅ Configured a **web server inside a private network**.  
✅ Set up **port forwarding (DNAT)** on Mikrotik to allow external access.  
✅ Secured the **firewall rules** to prevent unauthorized access.  
✅ Successfully tested **remote access to the internal server**.  
