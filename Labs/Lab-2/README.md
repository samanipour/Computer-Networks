# **Lab 2: Configuring VLANs**  
**Objective:**  
- Understand **Virtual LANs (VLANs)** and their benefits.  
- Configure VLANs on a **Mikrotik Router** and a **Managed Switch**.  
- Verify VLAN segmentation using **ping tests**.  

---

## **1. Overview of VLANs**  
### **What is a VLAN?**  
A **VLAN (Virtual Local Area Network)** is a logical network that **segments devices into groups**, even if they are connected to the same switch.  

### **Why Use VLANs?**  
- **Security**: Devices in different VLANs cannot communicate unless explicitly allowed.  
- **Performance**: Reduces network congestion by limiting broadcast traffic.  
- **Flexibility**: Devices can be grouped **logically** instead of being restricted by physical location.  

---

## **2. Lab Setup & Required Equipment**  
### **Equipment:**  
- **Mikrotik hAP ac lite Router**  
- **Managed Switch** (supports VLANs)  
- **Two PCs (one in VLAN 10, one in VLAN 20)**  
- **Ethernet cables**  
- **GNS3 (optional, for virtual lab)**  

### **Network Topology:**  

| VLAN | Network Address | Assigned Device | Switch Port | Router Interface |  
|------|---------------|----------------|--------------|------------------|  
| VLAN 10 (Sales) | 192.168.10.0/24 | PC1 (Sales) | ether2 | VLAN10 on ether1 |  
| VLAN 20 (IT) | 192.168.20.0/24 | PC2 (IT) | ether3 | VLAN20 on ether1 |  

- The **router’s ether1** will act as a **trunk port** (carrying VLAN-tagged traffic).  
- The **switch ports** will be assigned to specific VLANs.  

---

## **3. Step-by-Step Configuration**  
### **Step 1: Configure VLANs on the Managed Switch**  
#### **1.1 Access the Switch**  
1. Open **WinBox** or use **GNS3 CLI**.  
2. Navigate to **Bridge → VLANs**.  

#### **1.2 Create VLANs**  
- Add **VLAN 10 (Sales)**.  
- Add **VLAN 20 (IT)**.  

#### **1.3 Assign VLANs to Ports**  
- Assign **ether2** to **VLAN 10**.  
- Assign **ether3** to **VLAN 20**.  
- Set **ether1** as a **trunk port** (carries VLAN-tagged traffic).  

---

### **Step 2: Configure VLANs on the Mikrotik Router**  
#### **2.1 Open WinBox and Access the Router**  
1. Connect to the Mikrotik router via **WinBox**.  
2. Go to **Interfaces**.  

#### **2.2 Create VLAN Interfaces**  
- Add **VLAN 10** and **VLAN 20** to the router’s trunk interface (ether1):  

```
/interface vlan add name=VLAN10 vlan-id=10 interface=ether1
/interface vlan add name=VLAN20 vlan-id=20 interface=ether1
```

#### **2.3 Assign IP Addresses to VLAN Interfaces**  
- Assign an IP address for each VLAN:  

```
/ip address add address=192.168.10.1/24 interface=VLAN10
/ip address add address=192.168.20.1/24 interface=VLAN20
```

---

### **Step 3: Configure PCs with Static IPs**  
#### **3.1 Assign IP Addresses to PC1 (Sales - VLAN 10)**  
- IP Address: `192.168.10.2`  
- Subnet Mask: `255.255.255.0`  
- Gateway: `192.168.10.1`  

#### **3.2 Assign IP Addresses to PC2 (IT - VLAN 20)**  
- IP Address: `192.168.20.2`  
- Subnet Mask: `255.255.255.0`  
- Gateway: `192.168.20.1`  

---

### **Step 4: Verify VLAN Configuration**  
#### **4.1 Test VLAN Isolation**  
- **PC1 (VLAN 10)** should **NOT** be able to ping **PC2 (VLAN 20)**:  
  ```
  ping 192.168.20.2
  ```
  - If **ping fails**, VLAN isolation is working correctly.  

#### **4.2 Test VLAN IP Assignment**  
- Run `ipconfig /all` on each PC to verify the correct **VLAN IP**.  

---

## **4. Additional Configurations (Optional)**  
### **Enable VLAN Management on the Switch**  
- Assign an IP to the switch for management:  
  ```
  /ip address add address=192.168.1.100/24 interface=bridge1
  ```

### **Allow VLAN Communication via Router (Inter-VLAN Routing)**  
- If VLANs need to communicate, configure **Inter-VLAN Routing** (covered in **Lab 3**).  

---

## **5. Conclusion & Next Steps**  
### **What We Achieved:**  
✔ Created and configured **VLANs** on a **Mikrotik Router and Switch**.  
✔ Verified **network segmentation** by isolating traffic.  