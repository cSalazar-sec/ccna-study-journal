# VLANs, Trunking, and Router-on-a-Stick (ROAS)

---

# Lab Overview

This lab focuses on implementing:
- VLAN segmentation
- Trunk links between switches
- Inter-VLAN routing using Router-on-a-Stick (ROAS)

The objective is to create multiple isolated Layer 2 broadcast domains while still allowing communication between VLANs through Layer 3 routing.

---

# Lab Objectives

- Configure switch access ports
- Create and assign VLANs
- Configure trunk ports between switches
- Configure a native VLAN
- Allow only required VLANs across trunks
- Configure Router-on-a-Stick (ROAS)
- Configure router subinterfaces
- Verify inter-VLAN connectivity

---

# Network Topology

<img width="1028" height="543" alt="image" src="https://github.com/user-attachments/assets/2468d7cd-7606-43c1-8132-638a43bbc716" />


---

# What is a VLAN?

A:

```text
VLAN (Virtual Local Area Network)
```

logically separates devices into different broadcast domains even when they are connected to the same physical switch infrastructure.

Benefits include:
- Better segmentation
- Improved security
- Reduced broadcast traffic
- Easier management

Devices in different VLANs:
- Cannot communicate directly at Layer 2

They require:
- A Layer 3 device
- Routing between VLANs

---

# What is Trunking?

A:

```text
Trunk Port
```

carries traffic for multiple VLANs across a single link.

Trunks are commonly used:
- Between switches
- Between switches and routers
- Between switches and firewalls

VLAN traffic is identified using:

```text
802.1Q tagging
```

---

# What is Router-on-a-Stick (ROAS)?

Router-on-a-Stick is an inter-VLAN routing method where:

- One physical router interface
- Carries traffic for multiple VLANs
- Using multiple logical subinterfaces

Each subinterface:
- Belongs to a VLAN
- Has its own IP address
- Acts as the default gateway for that VLAN

---

# Lab Tasks

---

# Task 1 — Configure Access Ports

Configure switch interfaces connected to PCs as:

```text
Access Ports
```

and assign them to the correct VLANs.

---

## Example Configuration

```bash
interface f0/1
 switchport mode access
 switchport access vlan 10
```
<img width="284" height="39" alt="image" src="https://github.com/user-attachments/assets/c5050001-c45e-4fab-9118-00573e6679c0" />


---

# Why Access Ports Matter

Access ports:
- Carry traffic for only one VLAN
- Connect end devices like:
  - PCs
  - Printers
  - Phones

Devices connected to access ports send:
- Untagged frames

The switch internally associates those frames with the configured VLAN.

---

# Task 2 — Configure the Trunk Link

Configure the connection between:

```text
SW1 ↔ SW2
```

as a trunk link.

---

# Trunk Requirements

- Allow only necessary VLANs
- Configure an unused VLAN as the native VLAN
- Ensure all VLANs exist on both switches

---

# Example VLAN Creation

```bash
vlan 10
 name SALES

vlan 30
 name ENGINEERING

```
<img width="424" height="24" alt="image" src="https://github.com/user-attachments/assets/a6e7cdca-f055-4315-bcaa-7bd074c1abbe" />

---

# Example Trunk Configuration

```bash
interface g0/1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 1001
 switchport trunk allowed vlan 10,30
```
<img width="476" height="163" alt="image" src="https://github.com/user-attachments/assets/def752f5-b9ca-43ca-8369-7cb8952bbf09" />

---

# Why Use an Unused Native VLAN?

Using an unused VLAN as the native VLAN helps:
- Reduce VLAN hopping risks
- Improve security
- Limiting unnecesary traffic in the network
- Prevent accidental untagged traffic leakage

Best practice:
- Avoid using VLAN 1 as native VLAN

---

# Task 3 — Configure Router-on-a-Stick (ROAS)

Configure the connection between:

```text
SW2 ↔ R1
```

using:
- Router-on-a-Stick

---

# ROAS Design

The router uses:
- One physical interface
- Multiple subinterfaces

Each subinterface:
- Represents a VLAN
- Uses 802.1Q encapsulation
- Has a default gateway IP address

---

# Addressing Requirement

Assign:

```text
The last usable IP address
```

of each subnet to the router subinterfaces.

---

# Example ROAS Configuration

```bash
interface g0/0.10
 encapsulation dot1Q 10
 ip address 10.0.0.62 255.255.255.192
```

---

---

# Why Subinterfaces Matter

Subinterfaces allow:
- Multiple VLANs
- Multiple Layer 3 gateways
- Inter-VLAN routing
- VLAN separation with centralized routing

---

# Task 4 — Verify Connectivity

Test connectivity between PCs in different VLANs.

<img width="440" height="643" alt="image" src="https://github.com/user-attachments/assets/dd98aa9d-8fe5-457a-995e-58107aed73b9" />

---

# Verification Goals

All PCs should:
- Reach their default gateway
- Ping devices in other VLANs
- Successfully communicate through the router

---

# Verification Commands

---

## Verify VLANs

```bash
show vlan brief
```

---

## Verify Trunks

```bash
show interfaces trunk
```

---

## Verify Subinterfaces

```bash
show ip interface brief
```

---

## Verify Routing

```bash
show ip route
```

---

## Test Connectivity

```bash
ping <destination-ip>
```

Example:

```bash
ping 192.168.20.10
```

---

# Operational Concepts Reinforced

This lab reinforces several important CCNA concepts:

| Concept | Purpose |
|---|---|
| VLANs | Layer 2 segmentation |
| Access Ports | Connect endpoints to VLANs |
| Trunks | Carry multiple VLANs |
| Native VLAN | Handles untagged traffic |
| 802.1Q | VLAN tagging protocol |
| ROAS | Inter-VLAN routing |
| Subinterfaces | Logical VLAN gateways |

---

# Troubleshooting Mindset

When troubleshooting VLAN and ROAS issues, verify:

- VLANs exist on all switches
- Access ports are in the correct VLAN
- Trunk ports are operational
- Allowed VLANs are correct
- Native VLANs match
- Router subinterfaces are configured properly
- Encapsulation values match VLAN IDs
- PCs have the correct default gateway

Most VLAN issues come down to:
- VLAN mismatches
- Trunk misconfigurations
- Incorrect gateway assignments

---

# Key Takeaways

- VLANs create separate broadcast domains
- Access ports belong to a single VLAN
- Trunks carry multiple VLANs across one link
- ROAS enables inter-VLAN routing using one router interface
- Each VLAN requires a default gateway
- Native VLAN mismatches can create connectivity issues
- Proper trunk configuration is critical for VLAN communication
- Inter-VLAN communication requires Layer 3 routing
