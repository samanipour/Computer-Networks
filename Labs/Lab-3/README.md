# **Lab 3: Configuring Inter-VLAN Routing (Router-on-a-Stick)**
### **Objective:**  
- Understand **Inter-VLAN Routing** and why it is needed.  
- Configure **VLANs** on a **Mikrotik Router** using the **Router-on-a-Stick** method.  
- Enable **communication between VLANs** using **routing**.  

---

## **1. Overview of Inter-VLAN Routing**
### **What is VLAN?**
- A **VLAN (Virtual Local Area Network)** is used to **logically segment** a network into separate groups to improve security and performance.
- Devices in different **VLANs cannot communicate** unless routing is enabled.

### **Why is Inter-VLAN Routing Needed?**
- **By default, VLANs are isolated.**  
- To allow devices in **different VLANs to communicate**, we need a **Layer 3 device (router)** to route traffic between VLANs.

### **What is Router-on-a-Stick?**
- **Router-on-a-Stick** is a method where a **single physical router interface** is used to route traffic between multiple VLANs using **subinterfaces**.  
- The **router tags VLAN traffic** and routes it accordingly.

---

## **2. Lab Setup & Required Equipment**
### **Equipment:**
- **Mikrotik hAP ac lite Router**
- **Managed Switch** (supports VLANs)
- **Two PCs (one in VLAN 10, one in VLAN 20)**
- **Ethernet cables**
- **GNS3 or physical devices**

### **Network Topology:**  
| VLAN | Network Address | Assigned Device | Router Interface |  
|------|---------------|----------------|------------------|  
| VLAN 10 (Sales) | 192.168.10.0/24 | PC1 (Sales) | ether2 (VLAN 10) |  
| VLAN 20 (IT) | 192.168.20.0/24 | PC2 (IT) | ether3 (VLAN 20) |  

---

## **3. Step-by-Step Configuration**
### **Step 1: Configure VLANs on the Switch**
#### **1.1 Access Switch Configuration**
- Open **WinBox** or use **GNS3 CLI** to access the switch.

#### **1.2 Create VLANs**
- Go to **Bridge → VLANs** and add:
  - **VLAN 10 (Sales)**
  - **VLAN 20 (IT)**

#### **1.3 Assign VLANs to Ports**
- Assign **ether2** to **VLAN 10**.
- Assign **ether3** to **VLAN 20**.
- Set **ether1** as **trunk** (carries multiple VLANs).

### **Step 2: Configure VLAN Interfaces on Mikrotik Router**
#### **2.1 Open WinBox and Access the Router**
1. Open **WinBox** and connect to the **Mikrotik Router**.  
2. Navigate to **Interfaces**.  

#### **2.2 Create VLAN Subinterfaces**
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

### **Step 3: Configure PCs with Static IPs**
#### **3.1 Assign IP Addresses to PC1 (Sales - VLAN 10)**
- IP Address: `192.168.10.2`
- Subnet Mask: `255.255.255.0`
- Gateway: `192.168.10.1`

#### **3.2 Assign IP Addresses to PC2 (IT - VLAN 20)**
- IP Address: `192.168.20.2`
- Subnet Mask: `255.255.255.0`
- Gateway: `192.168.20.1`

### **Step 4: Enable Routing Between VLANs**
- By default, Mikrotik **routes traffic between directly connected networks**.  
- To **test routing**, ping between PCs.

```
ping 192.168.20.2  (from PC1)
ping 192.168.10.2  (from PC2)
```

### **Step 5: Verify Connectivity**
- If **ping works**, routing is correctly configured.  
- If **ping fails**, troubleshoot using:
  ```
  /ip route print
  ```
  ```
  /interface vlan print
  ```

---

## **4. Additional Configurations (Optional)**
### **Enable Firewall to Restrict Access Between VLANs**
- Block **PC1 from accessing PC2**:

```
/ip firewall filter add chain=forward src-address=192.168.10.2 dst-address=192.168.20.2 action=drop
```

- Allow only **specific services** (e.g., HTTP, SSH) between VLANs:

```
/ip firewall filter add chain=forward src-address=192.168.10.0/24 dst-address=192.168.20.0/24 protocol=tcp dst-port=80 action=accept
```

---

## **5. Conclusion & Next Steps**
### **What We Achieved:**
✔ Configured **VLANs** on a **Mikrotik router**.  
✔ Used **Router-on-a-Stick** to enable **Inter-VLAN Routing**.  
✔ Verified communication between VLANs.  
