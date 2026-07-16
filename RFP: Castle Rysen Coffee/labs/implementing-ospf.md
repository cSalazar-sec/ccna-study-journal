# Configuring OSPF (Single Area)

## Overview

The Coffee House district shop and the Fallout Shelter network are now connected through a point-to-point WAN link. Static routing has served its purpose, but maintaining routes manually doesn't scale as networks grow.

In this lab, I replaced manual routing with **Open Shortest Path First (OSPF)**, the most common Interior Gateway Protocol (IGP) used in enterprise networks.

Both routers will participate in **Area 0**, automatically exchanging routing information across the WAN while limiting unnecessary OSPF traffic on user-facing interfaces through the use of **passive interfaces**.

By the end of this lab, both sites should automatically learn each other's networks without relying on static routes.

---

## Lab Objectives

- Configure OSPF Area 0 on multiple routers.
- Advertise connected networks using network statements.
- Configure passive interfaces for user-facing networks.
- Verify OSPF neighbor adjacencies.
- Confirm routes are learned dynamically.
- Test end-to-end connectivity across the WAN.

---

## Network Diagram

<img width="1402" height="1122" alt="image" src="https://github.com/user-attachments/assets/7f2ebdfa-9211-40b9-87ae-ecc67a9c5536" />

---

# Task 1 — Verify the Initial Configuration

### Objective

Confirm both routers are ready for OSPF before advertising any networks.

### Steps

On **Cafe-R1**:

- Verify OSPF process **1** already exists.
- Confirm the Router ID is **1.1.1.1**.
- Ensure no network statements have been configured.
  
<img width="243" height="69" alt="image" src="https://github.com/user-attachments/assets/46d5c375-6912-401b-beef-fb1f0462dd04" />

- Verify the interface addressing:
  - **Ethernet0/0.10** → `10.0.18.1/27`
  - **Ethernet0/1** → `172.16.0.1/30`
    
<img width="340" height="137" alt="image" src="https://github.com/user-attachments/assets/c201856b-1cb0-4106-958e-a3ac0a38b3d5" />

Repeat the same verification on **Shelter-R2**.

Confirm:

- OSPF Process **1**
- Router ID **2.2.2.2**

<img width="243" height="69" alt="image" src="https://github.com/user-attachments/assets/173ecef1-0c3e-4372-bd45-c97093900293" />

- WAN interface:
  - **Ethernet0/1** → `172.16.0.2/30`

<img width="327" height="67" alt="image" src="https://github.com/user-attachments/assets/33671481-4a78-4a3d-83c5-4ac517253302" />

---

# Task 2 — Configure OSPF on Cafe-R1

### Objective

Enable OSPF Area 0 and advertise only the required networks.

The Coffee House router should advertise:

- Admin VLAN
- WAN link

However, the Admin VLAN should **not** send OSPF Hello packets toward end devices.

---

### Configuration

Enter OSPF configuration mode.

```cisco
router ospf 1
```

Advertise the Admin VLAN & WAN link.

<img width="445" height="52" alt="image" src="https://github.com/user-attachments/assets/034890b3-048c-4046-a4e3-58845896644e" />


Configure the Admin VLAN as a passive interface.

<img width="445" height="19" alt="image" src="https://github.com/user-attachments/assets/cb2986a6-eb7d-43ad-aca2-18bcc841f949" />

---

### Why Passive Interfaces?

A passive interface still advertises its connected network into OSPF.

The difference is that it **does not send or receive OSPF Hello packets**, preventing unnecessary neighbor discovery on networks that contain only end devices.

The WAN interface **must remain active** so it can establish an adjacency with the neighboring router.

---

# Task 3 — Configure OSPF on Shelter-R2

### Objective

Advertise the Fallout Shelter networks and establish an OSPF neighbor relationship across the WAN.

---

### Configuration

Advertise the WAN link & both Shelter VLANs.

<img width="473" height="69" alt="image" src="https://github.com/user-attachments/assets/eddf4178-7213-4723-a8ae-0275cfdcfb1e" />


Mark both user VLAN interfaces as passive.

<img width="471" height="37" alt="image" src="https://github.com/user-attachments/assets/9a664309-8f54-4550-942b-f0904291e6e0" />

---

# Task 4 — Verify OSPF Operation

### Objective

Confirm both routers have formed an adjacency and exchanged routes successfully.

---

### Verify Neighbor Relationship

Cafe-R1

<img width="617" height="71" alt="image" src="https://github.com/user-attachments/assets/c8270f91-3cf9-4d42-b284-e2ab1563f8c0" />

Shelter-R2

<img width="619" height="74" alt="image" src="https://github.com/user-attachments/assets/8634f6e4-a00c-4158-8ced-665a04c01992" />

Expected result:

- Neighbor State: **FULL**
- Neighbor ID matches the opposite router.

---

### Verify Learned Routes

Cafe-R1 should learn:

- 10.0.16.0/24
- 10.0.17.0/24

<img width="633" height="413" alt="image" src="https://github.com/user-attachments/assets/db696277-d0f2-4580-a77d-73c5c356d1dd" />

Shelter-R2 should learn:

- 10.0.18.0/27

<img width="666" height="311" alt="image" src="https://github.com/user-attachments/assets/582b3449-d3b9-4065-9bc5-7a6d22075501" />

---

### Verify OSPF Interfaces

Cafe-R1

<img width="588" height="71" alt="image" src="https://github.com/user-attachments/assets/31181c29-0d3f-439f-960c-fc3a2be52d5a" />

Shelter-R2

<img width="593" height="87" alt="image" src="https://github.com/user-attachments/assets/16defcd0-89b6-4ee6-aeff-24add631bfb7" />

Confirmed:

- WAN interface is actively participating in OSPF.
- User-facing interfaces are configured as passive.

Cafe-R1 - Interface e0/0.10

<img width="568" height="225" alt="image" src="https://github.com/user-attachments/assets/c06b5e15-092a-4908-ad32-2e28667516c2" />


Shelter-R2 - Interface e0/0.10

<img width="568" height="224" alt="image" src="https://github.com/user-attachments/assets/761ed027-f759-4bf3-ba82-5a22df38c505" />


Shelter-R2 - Interface e0/0.20

<img width="572" height="225" alt="image" src="https://github.com/user-attachments/assets/90bc3501-0726-48a3-b878-9fbdc32764c3" />

---

### Test Connectivity

From **Cafe-R1**:

<img width="588" height="171" alt="image" src="https://github.com/user-attachments/assets/91038970-b42d-42f9-b11e-effae1c5a505" />

From **Shelter-R2**:

<img width="547" height="88" alt="image" src="https://github.com/user-attachments/assets/dbf782b8-f00d-4f5f-8d26-06f015d5f1d1" />

All pings completed successfully.

---

# Understanding the Topology

## Before OSPF

```
Cafe-R1

Admin Network

      |

   Static Routes

      |

172.16.0.0/30

      |

Shelter-R2

Shelter VLANs
```

Every route had to be configured manually.

Adding new networks required additional static routes on each router.

---

## After OSPF

```
Cafe-R1

     OSPF Area 0

        <====>

172.16.0.0/30

        <====>

Shelter-R2
```

Both routers automatically exchange routing information.

New connected networks can be advertised simply by updating OSPF instead of creating additional static routes.

---

# Verification Checklist

- [X] OSPF process 1 is running on both routers.
- [X] Area 0 is configured correctly.
- [X] WAN interfaces establish an OSPF adjacency.
- [X] Neighbor state reaches **FULL**.
- [X] User-facing VLAN interfaces are configured as passive.
- [X] Cafe-R1 learns both Shelter VLAN routes.
- [X] Shelter-R2 learns the Coffee House Admin network.
- [X] End-to-end connectivity is successful.

---

# Key Takeaways

- OSPF is a **link-state routing protocol** that dynamically exchanges routing information between routers.
- Routers become neighbors by exchanging **Hello packets** on interfaces participating in the same OSPF area.
- **Area 0** is the backbone area and is required in every multi-area OSPF deployment.
- **Passive interfaces** advertise connected networks without attempting to form neighbor relationships with end devices.
- Routes learned through OSPF appear in the routing table with the code **O**.
- Dynamic routing greatly reduces administrative overhead compared to static routing, making OSPF ideal for medium and large enterprise networks.
