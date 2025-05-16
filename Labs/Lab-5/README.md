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

### **2.1 Create a RIP Instance**

Begin by creating a RIP instance on each router. This instance will define the types of routes to redistribute and other RIP-specific settings.

```bash
/routing rip instance
add name=default redistribute=connected,static originate-default=never
```

* `name=default` assigns a name to the RIP instance.
* `redistribute=connected,static` specifies that connected and static routes will be advertised.
* `originate-default=never` ensures that the default route is not advertised unless explicitly configured.

### **2.2 Configure Interface Templates**

Next, define interface templates to associate interfaces with the RIP instance. This replaces the previous method of adding networks directly.

#### **Router 1**

```bash
/routing rip interface-template
add interfaces=ether1 instance=default
add interfaces=ether2 instance=default
```

#### **Router 2**

```bash
/routing rip interface-template
add interfaces=ether1 instance=default
add interfaces=ether3 instance=default
add interfaces=ether4 instance=default
```

#### **Router 3**

```bash
/routing rip interface-template
add interfaces=ether1 instance=default
add interfaces=ether2 instance=default
```

*Note:* Replace `etherX` with the actual interface names corresponding to your network topology.

### **2.3 Verify RIP Configuration**

1. **Check Learned RIP Routes:**

   ```bash
   /ip route print where protocol=rip
   ```

This command displays routes learned via RIP.

2. **Test Connectivity:**

   From **PC1**, attempt to ping **PC3** (e.g., `ping 192.168.3.2`) to verify end-to-end connectivity through the RIP-configured routers.

---

## **Step 3: Configure OSPF (Open Shortest Path First)**

### **3.1 Enable OSPF on Router 1**

````bash
/routing ospf instance
set default router-id=1.1.1.1:contentReference[oaicite:9]{index=9}

```bash
/routing ospf interface-template
add interfaces=ether1 area=backbone
add interfaces=ether2 area=backbone:contentReference[oaicite:12]{index=12}
````

### **3.2 Enable OSPF on Router 2**

````bash
/routing ospf instance
set default router-id=2.2.2.2:contentReference[oaicite:15]{index=15}

```bash
/routing ospf interface-template
add interfaces=ether1 area=backbone
add interfaces=ether3 area=backbone
add interfaces=ether4 area=backbone:contentReference[oaicite:18]{index=18}
````

### **3.3 Enable OSPF on Router 3**

````bash
/routing ospf instance
set default router-id=3.3.3.3:contentReference[oaicite:21]{index=21}

```bash
/routing ospf interface-template
add interfaces=ether1 area=backbone
add interfaces=ether2 area=backbone:contentReference[oaicite:24]{index=24}
````

*Note:* Replace `etherX` with the actual interface names corresponding to your network topology.

---

### **Verify OSPF Configuration**

1. **Check OSPF Neighbor Relationships:**

   ```bash
   /routing ospf neighbor print
   ```

   This command displays the current OSPF neighbors and their states.

2. **Test Connectivity:**

   From **PC1**, attempt to ping **PC3** (e.g., `ping 192.168.3.2`) to verify end-to-end connectivity through the OSPF-configured routers.

---

## **4. Conclusion & Next Steps**  
### **What We Achieved:**  
✔ Configured **RIP and OSPF** on three Mikrotik routers.  
✔ Verified routing using **ping tests** and **route tables**.  