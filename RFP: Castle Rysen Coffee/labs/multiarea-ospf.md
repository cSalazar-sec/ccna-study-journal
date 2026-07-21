# Configuring Multi-Area OSPF

## Overview

As the Coffee House network expands, keeping every router inside a single OSPF area becomes less efficient. Every topology change must be flooded throughout the entire domain, increasing CPU utilization, memory consumption, and convergence time.

To improve scalability, the Fallout Shelter will become an **Area Border Router (ABR)**, separating the network into two OSPF areas.

- **Area 0** will remain the backbone, containing the shelter infrastructure.
- **Area 1** will contain the Coffee House network.
- The shelter will summarize its internal networks before advertising them to the café.
- Finally, the shelter will advertise a default route so the Coffee House automatically knows how to reach the Internet.

This design reduces routing overhead while keeping the network scalable and easy to manage.

---

## Lab Objectives

- Configure a multi-area OSPF topology.
- Convert a router into an Area Border Router (ABR).
- Move interfaces between OSPF areas.
- Configure inter-area route summarization.
- Advertise a default route into OSPF.
- Verify inter-area routing and Internet reachability.

---

## Network Diagram

<img width="1708" height="921" alt="image" src="https://github.com/user-attachments/assets/1dfd7e42-313e-4c76-a38b-8719c7b28d67" />

---

# Task 1 — Create the Area Boundary

### Objective

Convert **Shelter-R1** into an **Area Border Router** by separating the Coffee House and Fallout Shelter into different OSPF areas.

The final design should be:

| Network | Area |
|----------|------|
| Shelter VLANs | Area 0 |
| WAN Link | Area 1 |
| Coffee House LAN | Area 1 |

---

### Verify the Current Configuration

Before making any changes, I inspected the current OSPF interface assignments.

show ip ospf interface brief

<img width="653" height="93" alt="image" src="https://github.com/user-attachments/assets/fa0d1754-ec9e-4bc3-82a7-ab388ae20fc4" />
<img width="635" height="74" alt="image" src="https://github.com/user-attachments/assets/4bf03af9-97e3-4bda-9b93-44c8c8eb62ac" />

show ip protocols

<img width="524" height="510" alt="image" src="https://github.com/user-attachments/assets/c0a5bb86-88bc-47d9-aacb-d1757e217af7" />
<img width="533" height="486" alt="image" src="https://github.com/user-attachments/assets/e2252690-b75b-45ca-b7b0-39c112a7482b" />

---

### Configure Shelter-R1

Leave the shelter VLAN interfaces in **Area 0**.

Move the interface facing **Cafe-R1** into **Area 1**.

<img width="914" height="112" alt="image" src="https://github.com/user-attachments/assets/a6c0da08-fc48-4d27-b816-91625aaf1e63" />

---

### Configure Cafe-R1

Move both the LAN and WAN interfaces into **Area 1**.

<img width="511" height="72" alt="image" src="https://github.com/user-attachments/assets/c70378d5-3058-4fa6-b3c7-e26f45c59c55" />

---

### Verify Neighbor Formation

Shelter-R1:

<img width="665" height="75" alt="image" src="https://github.com/user-attachments/assets/0a6492e6-4e27-4856-83d2-c2732c884de8" />

Cafe-R1:

<img width="662" height="75" alt="image" src="https://github.com/user-attachments/assets/280ac852-979d-4397-b5d6-5bd3516836a8" />

The adjacency should reform across the WAN after both routers are placed into Area 1.

---

# Task 2 — Summarize the Shelter Networks

### Objective

Reduce the number of inter-area routes advertised toward the Coffee House.

Instead of sending individual routes for every Shelter VLAN, the ABR will advertise a single summary route.

---

### Identify the Summary

The shelter networks are:

```
10.0.16.0/24
10.0.17.0/24
```

These contiguous networks can be summarized as:

```
10.0.16.0/23
```

---

### Configure the Summary

On **Shelter-R1**:

<img width="534" height="39" alt="image" src="https://github.com/user-attachments/assets/4c147cd8-7f70-4fd3-90bc-e92bea090d92" />

This causes the ABR to advertise a single summary route into Area 1.

---

### Verify the Summary

On **Cafe-R1**:

<img width="676" height="412" alt="image" src="https://github.com/user-attachments/assets/34da96d5-5315-4f8a-a97b-1bf3108ebf9b" />

Instead of seeing:

```
10.0.16.0/24
10.0.17.0/24
```

You now see:

```
10.0.16.0/23
```

The summarized route should appear as an **inter-area** route.

---

# Task 3 — Advertise the Default Route

### Objective

Allow the Coffee House to automatically learn the path toward the Internet.

The Shelter router already has a static default route pointing toward the ISP.

Instead of configuring a default route manually on every router, OSPF can advertise it automatically.

---

### Verify the Static Route

On **Shelter-R1**:

```cisco
show ip route
```

We already have a default route :

<img width="666" height="537" alt="image" src="https://github.com/user-attachments/assets/a8433fe0-2cde-4dbd-941b-97bba69fd347" />

---

### Advertise the Default Route

Configure OSPF to originate the default route.

<img width="482" height="35" alt="image" src="https://github.com/user-attachments/assets/f1710d71-88b7-4e35-a457-631c02245bb6" />

---

### Verify on Cafe-R1

Prior to the change. No default route.

<img width="549" height="344" alt="image" src="https://github.com/user-attachments/assets/11157e81-5d4f-4841-b320-2d24f7e1f197" />

Display the routing table(Post-change).

<img width="567" height="353" alt="image" src="https://github.com/user-attachments/assets/c3a16522-7161-410b-beab-ff04f5443d2a" />

The route should point toward **Shelter-R1**.

---

### Verify Reachability

Test connectivity toward the ISP.

<img width="600" height="89" alt="image" src="https://github.com/user-attachments/assets/c6908403-aea1-4eb5-afe7-ce86dc164961" />

Traffic successfully traverse:

```
Cafe-R1

↓

Shelter-R1

↓

ISP
```

---

# Understanding the Topology

## Before Multi-Area OSPF

```
                Area 0

Cafe-R1 ---------------- Shelter-R1

Coffee LAN      Shelter VLANs
```

Every network belongs to the same OSPF area.

All LSAs are flooded throughout the entire routing domain.

---

## After Multi-Area OSPF

```
             Area 1

Cafe-R1
     |
     |
-------------------------
        ABR
     Shelter-R1
-------------------------

Area 0

Shelter VLANs
```

The Area Border Router separates the routing domain while maintaining communication between both areas.

---

## Route Summarization

Without summarization:

```
10.0.16.0/24
10.0.17.0/24
```

With summarization:

```
10.0.16.0/23
```

Fewer LSAs result in:

- Smaller routing tables
- Faster convergence
- Lower CPU utilization
- Better scalability

---

# Verification Checklist

- [X] Shelter-R1 is operating as an Area Border Router.
- [X] Shelter VLANs remain in Area 0.
- [X] Coffee House interfaces belong to Area 1.
- [X] OSPF neighbor adjacency returns to the FULL state.
- [X] Shelter networks are summarized into a single inter-area route.
- [X] Cafe-R1 receives the summarized route.
- [X] Shelter advertises the default route into OSPF.
- [X] Cafe-R1 installs the default route automatically.
- [X] Internet connectivity is verified through Shelter-R1.

---

# Key Takeaways

- An **Area Border Router (ABR)** connects two or more OSPF areas and maintains a separate link-state database for each area.
- **Area 0** is the OSPF backbone, and all non-backbone areas must connect to it through an ABR.
- **Route summarization** on an ABR reduces the number of LSAs exchanged between areas, resulting in smaller routing tables and improved scalability.
- The **`area range`** command summarizes routes only when configured on an ABR.
- The **`default-information originate`** command advertises an existing static default route into OSPF, allowing downstream routers to automatically learn the gateway of last resort.
- Multi-area OSPF is the preferred design for medium and large enterprise networks because it improves scalability, stability, and convergence performance.
