# **Lab 5: Configuring Dynamic Routing with RIP and OSPF on Mikrotik Routers**  

## **Objective**  
- Understand **Dynamic Routing Protocols** and why they are used.  
- Configure **RIP (Routing Information Protocol)** and **OSPF (Open Shortest Path First)** on **Mikrotik Routers**.  
- Verify routing and network connectivity using **ping tests** and **route tables**.  

---

## **1. Overview of Dynamic Routing**  
### **What is Dynamic Routing?**  
Dynamic routing **automatically updates and manages routes** between networks. Routers exchange information to learn the best path to other networks without manual intervention.  

### **Why Use Dynamic Routing?**  
- **Scalability**: Suitable for larger networks.  
- **Automatic Route Updates**: No need to manually configure routes.  
- **Redundancy & Load Balancing**: Adapts to network failures and optimizes traffic paths.  

### **Types of Dynamic Routing Protocols:**  
| Protocol | Type | Best for | Convergence Speed |  
|----------|------|----------|------------------|  
| RIP (Routing Information Protocol) | Distance Vector | Small networks | Slow |  
| OSPF (Open Shortest Path First) | Link-State | Large, complex networks | Fast |  

---

## **2. Lab Setup & Required Equipment**  
### **Equipment:**  
- **Three Mikrotik hAP ac lite Routers**  
- **Three PCs (one in each network)**  
- **Ethernet cables**  
- **GNS3 (optional, for virtual lab)**  

### **Network Topology:**  

| Network | Subnet | Assigned Device | Router Interface |  
|---------|--------|----------------|------------------|  
| Network 1 | 192.168.1.0/24 | PC1 | ether2 on Router 1 |  
| Network 2 | 192.168.2.0/24 | PC2 | ether2 on Router 2 |  
| Network 3 | 192.168.3.0/24 | PC3 | ether2 on Router 3 |  
| Router 1 to Router 2 | 10.0.0.0/30 | Router 1 (10.0.0.1) | ether1 (connected to Router 2) |  
|  |  | Router 2 (10.0.0.2) | ether1 (connected to Router 1) |  
| Router 2 to Router 3 | 10.0.0.4/30 | Router 2 (10.0.0.5) | ether3 (connected to Router 3) |  
|  |  | Router 3 (10.0.0.6) | ether1 (connected to Router 2) |  

---

## **3. Step-by-Step Configuration**  

### **Step 1: Configure Basic IP Addressing on Each Router**  
#### **1.1 Assign IP Addresses to Router 1**  
1. Open **WinBox** and connect to **Router 1**.  
2. Assign an IP address to **ether1 (WAN - Connected to Router 2)**:  
   ```
   /ip address add address=10.0.0.1/30 interface=ether1
   ```
3. Assign an IP address to **ether2 (LAN - Network 1)**:  
   ```
   /ip address add address=192.168.1.1/24 interface=ether2
   ```
4. Enable the interfaces:  
   ```
   /interface enable ether1
   /interface enable ether2
   ```

#### **1.2 Assign IP Addresses to Router 2**  
1. Open **WinBox** and connect to **Router 2**.  
2. Assign an IP address to **ether1 (Connected to Router 1)**:  
   ```
   /ip address add address=10.0.0.2/30 interface=ether1
   ```
3. Assign an IP address to **ether2 (LAN - Network 2)**:  
   ```
   /ip address add address=192.168.2.1/24 interface=ether2
   ```
4. Assign an IP address to **ether3 (Connected to Router 3)**:  
   ```
   /ip address add address=10.0.0.5/30 interface=ether3
   ```

#### **1.3 Assign IP Addresses to Router 3**  
1. Open **WinBox** and connect to **Router 3**.  
2. Assign an IP address to **ether1 (Connected to Router 2)**:  
   ```
   /ip address add address=10.0.0.6/30 interface=ether1
   ```
3. Assign an IP address to **ether2 (LAN - Network 3)**:  
   ```
   /ip address add address=192.168.3.1/24 interface=ether2
   ```

---

## **Step 2: Configure RIP (Routing Information Protocol)**  
### **Enable RIP on All Routers**  

#### **2.1 Enable RIP on Router 1**  
```
/routing rip interface add interface=ether1
/routing rip network add network=192.168.1.0/24
/routing rip network add network=10.0.0.0/30
```

#### **2.2 Enable RIP on Router 2**  
```
/routing rip interface add interface=ether1
/routing rip interface add interface=ether3
/routing rip network add network=192.168.2.0/24
/routing rip network add network=10.0.0.0/30
/routing rip network add network=10.0.0.4/30
```

#### **2.3 Enable RIP on Router 3**  
```
/routing rip interface add interface=ether1
/routing rip network add network=192.168.3.0/24
/routing rip network add network=10.0.0.4/30
```

### **Verify RIP Configuration**  
1. Check the learned RIP routes:  
   ```
   /ip route print
   ```
2. Ping from **PC1 to PC3 (192.168.3.2)** to test connectivity.

---

## **Step 3: Configure OSPF (Open Shortest Path First)**  
### **Enable OSPF on All Routers**  

#### **3.1 Enable OSPF on Router 1**  
```
/routing ospf instance set default router-id=1.1.1.1
/routing ospf network add network=192.168.1.0/24 area=backbone
/routing ospf network add network=10.0.0.0/30 area=backbone
```

#### **3.2 Enable OSPF on Router 2**  
```
/routing ospf instance set default router-id=2.2.2.2
/routing ospf network add network=192.168.2.0/24 area=backbone
/routing ospf network add network=10.0.0.0/30 area=backbone
/routing ospf network add network=10.0.0.4/30 area=backbone
```

#### **3.3 Enable OSPF on Router 3**  
```
/routing ospf instance set default router-id=3.3.3.3
/routing ospf network add network=192.168.3.0/24 area=backbone
/routing ospf network add network=10.0.0.4/30 area=backbone
```

### **Verify OSPF Configuration**  
1. Check OSPF neighbor relationships:  
   ```
   /routing ospf neighbor print
   ```
2. Test connectivity from **PC1 to PC3**.

---

## **4. Conclusion & Next Steps**  
### **What We Achieved:**  
✔ Configured **RIP and OSPF** on three Mikrotik routers.  
✔ Verified routing using **ping tests** and **route tables**.  