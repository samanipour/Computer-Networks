# **Lab 2: Creating VLANs for Network Segmentation**  

## **1. Lab Description and Objectives**  

### **Lab Description:**  
In this lab, students will learn about **Virtual Local Area Networks (VLANs)** and how they improve network segmentation. VLANs allow logical grouping of devices regardless of their physical location, improving **security, manageability, and performance**. Students will configure VLANs on a **Mikrotik hAP lite router (real-world setup)** and on a **network switch (GNS3 simulation)** to understand VLAN-based segmentation.  

### **Objectives:**  
By the end of this lab, students should be able to:  
✅ Understand VLAN concepts and benefits.  
✅ Create and configure VLANs on a **Mikrotik hAP lite router**.  
✅ Configure VLANs using a **Layer 2 Switch (in GNS3)**.  
✅ Assign VLANs to different interfaces and verify connectivity.  

---

## **2. Lab Setup and Instructions Using Real-World Devices (Mikrotik hAP lite Router)**  

### **2.1 Required Equipment:**  
- **Mikrotik hAP lite Router**  
- **Two or more PCs/Laptops**  
- **Ethernet cables**  

### **2.2 Network Topology (Real-World Setup Using hAP lite)**  
```
+--------+       +------------------+       +--------+
|  PC 1  |-------| Mikrotik hAP lite|-------|  PC 2  |
+--------+       +------------------+       +--------+
                      |      |
                      |      |
                  VLAN 10  VLAN 20
```
PC 1 belongs to VLAN 10, and PC 2 belongs to VLAN 20.  

---

### **2.3 Step-by-Step Instructions (Real-World Setup with hAP lite)**  

#### **Step 1: Physical Connection**  
1. Connect **PC 1** to **Ether2** of the Mikrotik router.  
2. Connect **PC 2** to **Ether3** of the Mikrotik router.  

#### **Step 2: Configure VLANs on Mikrotik hAP lite**  

1. **Access the router via WinBox or SSH** (`192.168.88.1`).  

2. **Create VLAN Interfaces**:  
   Open **New Terminal** and enter the following commands:  
   ```sh
   /interface vlan add name=vlan10 vlan-id=10 interface=ether2
   /interface vlan add name=vlan20 vlan-id=20 interface=ether3
   ```

3. **Assign IP Addresses to VLANs**:  
   ```sh
   /ip address add address=192.168.10.1/24 interface=vlan10
   /ip address add address=192.168.20.1/24 interface=vlan20
   ```

4. **Configure DHCP Server for VLANs (Optional)**:  
   - Create **IP pools**:  
     ```sh
     /ip pool add name=pool_vlan10 ranges=192.168.10.100-192.168.10.200
     /ip pool add name=pool_vlan20 ranges=192.168.20.100-192.168.20.200
     ```
   - Create **DHCP servers**:  
     ```sh
     /ip dhcp-server add name=dhcp10 interface=vlan10 address-pool=pool_vlan10 disabled=no
     /ip dhcp-server add name=dhcp20 interface=vlan20 address-pool=pool_vlan20 disabled=no
     ```
   - Assign DHCP **network settings**:  
     ```sh
     /ip dhcp-server network add address=192.168.10.0/24 gateway=192.168.10.1
     /ip dhcp-server network add address=192.168.20.0/24 gateway=192.168.20.1
     ```

#### **Step 3: Configure PC 1 and PC 2 with Static IPs**  
- **PC 1 Configuration (VLAN 10)**:  
  - IP Address: `192.168.10.10`  
  - Subnet Mask: `255.255.255.0`  
  - Gateway: `192.168.10.1`  

- **PC 2 Configuration (VLAN 20)**:  
  - IP Address: `192.168.20.10`  
  - Subnet Mask: `255.255.255.0`  
  - Gateway: `192.168.20.1`  

#### **Step 4: Test VLAN Connectivity**  
- **PC 1 should NOT be able to ping PC 2**:  
  ```sh
  ping 192.168.20.10
  ```  
- **PC 1 should be able to ping its gateway**:  
  ```sh
  ping 192.168.10.1
  ```  
- **PC 2 should be able to ping its gateway**:  
  ```sh
  ping 192.168.20.1
  ```

---

## **3. Lab Setup and Instructions Using GNS3 Simulator**  

### **3.1 Required GNS3 Devices:**  
- **Mikrotik Router (CHR)**  
- **Layer 2 Network Switch**  
- **Three Virtual PCs (VPCS)**  

### **3.2 Network Topology in GNS3:**  
```
+--------+        +-----------+        +--------------+
|  PC 1  |--------| Switch    |--------| Mikrotik CHR |
+--------+        | VLAN 10   |        | Router       |
|  PC 2  |--------| VLAN 20   |        +--------------+
+--------+        +-----------+
```

### **3.3 Step-by-Step Instructions in GNS3**  

#### **Step 1: Creating the Topology**  
1. Open **GNS3** and create a new project.  
2. Drag and drop the following devices:  
   - **Mikrotik Router (CHR)**  
   - **Network Switch (Layer 2)**  
   - **Three Virtual PCs (VPCS)**  
3. Connect the devices:  
   - **PC 1 → Switch (VLAN 10)**  
   - **PC 2 → Switch (VLAN 20)**  
   - **Switch → Mikrotik Router**  

#### **Step 2: Configure VLANs on the Switch**  
1. Open the switch terminal and enter:  
   ```sh
   vlan database
   vlan 10 name VLAN10
   vlan 20 name VLAN20
   exit
   ```

2. Assign VLANs to switch ports:  
   ```sh
   interface FastEthernet0/1
   switchport mode access
   switchport access vlan 10
   exit

   interface FastEthernet0/2
   switchport mode access
   switchport access vlan 20
   exit
   ```

#### **Step 3: Configure VLANs on Mikrotik Router**  
1. Open the router terminal and configure VLANs:  
   ```sh
   /interface vlan add name=vlan10 vlan-id=10 interface=ether1
   /interface vlan add name=vlan20 vlan-id=20 interface=ether1
   ```

2. Assign IP addresses:  
   ```sh
   /ip address add address=192.168.10.1/24 interface=vlan10
   /ip address add address=192.168.20.1/24 interface=vlan20
   ```

#### **Step 4: Configure Virtual PCs (VPCS)**  
- **PC 1 (VLAN 10):**  
  ```sh
  ip 192.168.10.10 255.255.255.0 192.168.10.1
  ```  
- **PC 2 (VLAN 20):**  
  ```sh
  ip 192.168.20.10 255.255.255.0 192.168.20.1
  ```

#### **Step 5: Test Connectivity**  
- **PC 1 should NOT be able to ping PC 2**.  
- **PC 1 should be able to ping its gateway (192.168.10.1)**.  
- **PC 2 should be able to ping its gateway (192.168.20.1)**.  

---

## **4. Lab Exercises (Using GNS3)**  
1. **Try enabling inter-VLAN routing** by adding a router-on-a-stick configuration.  
2. **Configure VLAN trunking** on the switch and test communication.  
3. **Enable DHCP** for VLANs and verify that PCs get IPs dynamically.  