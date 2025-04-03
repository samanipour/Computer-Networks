# **Lab 1: Creating a Basic LAN (Local Area Network)**  
## **1. Lab Description and Objectives**  

### **Lab Description:**  
In this lab, students will set up a simple Local Area Network (**LAN**) using a **Mikrotik hAP lite router** and a network switch (for GNS3 simulation). They will configure **IP addresses**, enable **basic connectivity**, and test the network using various commands like `ping` and `traceroute`.  

### **Objectives:**  
By the end of this lab, students should be able to:  
✅ Understand the concept of LAN and its components.  
✅ Assign **static IP addresses** to devices.  
✅ Set up a **basic network topology** using physical and simulated devices.  
✅ Verify connectivity between devices.  

---

## **2. Lab Setup and Instructions Using Real-World Devices (Mikrotik hAP lite)**  

### **2.1 Required Equipment:**  
- **Mikrotik hAP lite Router**  
- **Two PCs/Laptops**  
- **Ethernet cables**  

### **2.2 Network Topology:**  
```
+--------+        +--------------------+        +--------+
|  PC 1  |--------|  Mikrotik hAP lite |--------|  PC 2  |
+--------+        +--------------------+        +--------+
```
  
### **2.3 Step-by-Step Instructions:**  

#### **Step 1: Physical Connection**  
1. Connect **PC 1** to **Ether1** of the Mikrotik hAP lite router.  
2. Connect **PC 2** to **Ether2** of the Mikrotik hAP lite router.  

#### **Step 2: Configure Mikrotik Router**  

1. Access the Mikrotik router:  
   - Connect via **WinBox** or SSH (`192.168.88.1`).  
   - Login with default credentials (**admin**, no password).  

2. Assign IP addresses to the router:  
   - Open **New Terminal** in WinBox and enter:  
     ```sh
     /ip address add address=192.168.1.1/24 interface=ether1
     /ip address add address=192.168.1.2/24 interface=ether2
     ```  

#### **Step 3: Configure PC 1 and PC 2 with Static IPs**  
- **PC 1 Configuration:**  
  - IP Address: `192.168.1.10`  
  - Subnet Mask: `255.255.255.0`  
  - Gateway: `192.168.1.1`  

- **PC 2 Configuration:**  
  - IP Address: `192.168.1.20`  
  - Subnet Mask: `255.255.255.0`  
  - Gateway: `192.168.1.2`  

#### **Step 4: Test Connectivity**  
- From **PC 1**, open the command prompt and **ping** PC 2:  
  ```sh
  ping 192.168.1.20
  ```  
- From **PC 2**, **ping** PC 1:  
  ```sh
  ping 192.168.1.10
  ```  
- Run `tracert` to check the path between devices.  

---

## **3. Lab Setup and Instructions Using GNS3 Simulator**  

### **3.1 Required GNS3 Devices:**  
- **Mikrotik Router (CHR)**  
- **Network Switch**  
- **Two Virtual PCs (VPCS)**  

### **3.2 Network Topology in GNS3:**  
```
+--------+        +----------+        +--------------+        +----------+        +--------+
|  PC 1  |--------|  Switch1 |--------| Mikrotik CHR |--------|  Switch2 |--------|  PC 2  |
+--------+        +----------+        +--------------+        +----------+        +--------+
```
  
### **3.3 Step-by-Step Instructions in GNS3**  

#### **Step 1: Creating the Topology**  
1. **Open GNS3** and create a new project.  
2. Drag and drop the following devices:  
   - **Mikrotik Router (CHR)**  
   - **Network Switch**  
   - **Two Virtual PCs (VPCS)**  
3. Connect the devices:  
   - **PC 1 → Switch1 → Mikrotik Router → Switch2 → PC 2**  

#### **Step 2: Configure the Mikrotik Router**  
1. Start the router and access the terminal.  
2. Assign IP addresses:  
   ```sh
   /ip address add address=192.168.1.1/24 interface=ether1
   ```  
   ```sh
   /ip address add address=192.168.2.2/24 interface=ether2
   ```  

#### **Step 3: Configure PC 1 and PC 2 in GNS3**  
- Open the **VPCS Terminal** and assign static IPs:  
  - **PC 1:**  
    ```sh
    ip 192.168.1.10 255.255.255.0 192.168.1.1
    ```  
  - **PC 2:**  
    ```sh
    ip 192.168.2.20 255.255.255.0 192.168.2.2
    ```  

#### **Step 4: Test Connectivity**  
1. **Ping between PCs:**  
   - From **PC 1**, run:  
     ```sh
     ping 192.168.2.20
     ```  
   - From **PC 2**, run:  
     ```sh
     ping 192.168.1.10
     ```  
2. **Run Traceroute** to check network path:  
   ```sh
   trace 192.168.2.20
   ```

---

## **4. Lab Exercises (Using GNS3)**  

### **Exercise 1: Verify Connectivity**  
- Use `ping` to test communication between devices.  
- Use `trace` to check the routing path.  

### **Exercise 2: Assign Dynamic IPs (Bonus Task)**  
- Instead of static IPs, configure **DHCP on the Mikrotik router**:  
  ```sh
  /ip dhcp-server add interface=ether1 address-pool=dhcp_pool
  /ip pool add name=dhcp_pool ranges=192.168.1.50-192.168.1.100
  /ip dhcp-server network add address=192.168.1.0/24 gateway=192.168.1.1
  ```  
- Configure **PC 1 and PC 2** to obtain IPs dynamically and verify using `ipconfig` or `ifconfig`.  

---

## **5. Summary**  
✅ **Successfully set up a basic LAN using Mikrotik Router and Switch (GNS3).**  
✅ **Configured static and dynamic IP addresses.**  
✅ **Tested connectivity with `ping` and `traceroute`.**  