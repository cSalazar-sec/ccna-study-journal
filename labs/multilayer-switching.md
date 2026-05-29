# Multilayer Switching

---

# Lab Overview

This lab focuses on migrating from:
- Router-on-a-Stick (ROAS)

to:

```text
Multilayer Switching
```

using a Layer 3 switch.

Instead of relying on a router for inter-VLAN routing, SW2 will now perform Layer 3 routing locally using:
- SVIs (Switched Virtual Interfaces)
- IP routing
- A routed point-to-point connection to R1

This design improves:
- Performance
- Scalability
- Routing efficiency

and reflects how modern enterprise campus networks are commonly deployed.

---

# Lab Objectives

- Replace ROAS with multilayer switching
- Configure routed Layer 3 interfaces
- Configure SVIs for VLAN routing
- Configure a default route on the multilayer switch
- Verify inter-VLAN communication
- Verify Internet connectivity

---

# Existing Topology

Initial design:
- SW1 ↔ SW2 connected via trunk
- SW2 ↔ R1 connected using ROAS

Updated design:
- SW1 ↔ SW2 remains a Layer 2 trunk
- SW2 ↔ R1 becomes a Layer 3 routed link

---

# Network Diagram

<img width="1164" height="566" alt="image" src="https://github.com/user-attachments/assets/7207f91f-2535-47f8-92ca-850ad69377d1" />

---

# What is a Multilayer Switch?

A:

```text
Multilayer Switch
```

is a switch capable of performing:
- Layer 2 switching
- Layer 3 routing

This allows the switch to:
- Route traffic between VLANs locally
- Eliminate router bottlenecks
- Improve performance

---

# Why Replace ROAS?

Router-on-a-Stick works well in:
- Small environments
- Labs
- Simpler topologies

However, ROAS creates a limitation:

```text
All inter-VLAN traffic must traverse a single router interface.
```

This can become:
- A bottleneck
- Less scalable
- Less efficient

Multilayer switching solves this by routing directly on the switch hardware.

---

# Task 1 — Replace ROAS with a Layer 3 Connection

Remove the existing ROAS configuration between:

```text
R1 ↔ SW2
```
<img width="674" height="431" alt="image" src="https://github.com/user-attachments/assets/c169959b-5855-43c7-b952-99fe57de3743" />

and replace it with a:

```text
Point-to-Point Layer 3 Link
```

---

# Layer 3 Routed Port Configuration

A switchport must first be converted into a routed port.

---

## Example Configuration on SW2

```bash
interface g1/0/2
 no switchport
 ip address 10.0.0.193 255.255.255.252
```

<img width="652" height="182" alt="image" src="https://github.com/user-attachments/assets/bd76a250-3631-40fc-a4df-e4fca1123a1e" />

---

## Example Configuration on R1

```bash
interface g0/0
 ip address 10.0.0.194 255.255.255.252
```

<img width="570" height="53" alt="image" src="https://github.com/user-attachments/assets/5db9c227-de2e-4e6c-a77c-e5fe01b47f4b" />

---

# Why Use Routed Ports?

Routed ports:
- Operate at Layer 3
- Do not belong to VLANs
- Behave like normal router interfaces

They are commonly used:
- Between multilayer switches
- Between switches and routers
- In enterprise distribution/core designs

---

# Configure the Default Route on SW2

Since SW2 now performs routing, it needs a route toward external networks.

---

## Example Default Route

```bash
ip route 0.0.0.0 0.0.0.0 10.0.0.194
```

Meaning:

```text
"If no more specific route exists, send traffic to R1."
```
<img width="566" height="215" alt="image" src="https://github.com/user-attachments/assets/bdaaaefa-db34-4de7-be2a-fb07994909fc" />

---

# Task 2 — Configure SVIs

Configure:
- One SVI per VLAN

Each SVI will act as:
- The default gateway for its VLAN

---

# What is an SVI?

An:

```text
SVI (Switched Virtual Interface)
```

is a virtual Layer 3 interface associated with a VLAN.

SVIs allow multilayer switches to:
- Route between VLANs
- Provide default gateways
- Perform Layer 3 services

---

# Addressing Requirement

Assign:

```text
The last usable IP address
```

of each subnet to the appropriate SVI.

---

# Enable Layer 3 Routing

Multilayer switches require:

```bash
ip routing
```

Without this command:
- The switch behaves only as a Layer 2 device
- Inter-VLAN routing will not occur

---

# Example Full SVI Configuration

```bash

interface vlan 10
 ip address 10.0.0.62 255.255.255.192

interface vlan 20
 ip address 10.0.0.126 255.255.255.192

interface vlan 30
 ip address 10.0.0.190 255.255.255.192
 
```
<img width="564" height="237" alt="image" src="https://github.com/user-attachments/assets/f2fec7f2-4d28-4f80-9833-57bd8f13023d" />


```text
SVIs are all up
```

<img width="524" height="39" alt="image" src="https://github.com/user-attachments/assets/1a683105-a979-458d-a888-da69e3e2892d" />

---

# Task 3 — Test Inter-VLAN Connectivity

Verify that devices in different VLANs can communicate.

---

# Verification Goals

Devices should be able to:
- Reach their SVI gateway
- Reach devices in other VLANs
- Successfully route through SW2 to R1
  
<img width="450" height="760" alt="image" src="https://github.com/user-attachments/assets/4ddbe454-da8e-4042-8f60-c2cc312a2a47" />


---

# Verification Commands

## Verify VLAN Interfaces

```bash
show ip interface brief
```

---

## Verify Routing Table

```bash
show ip route
```

---

## Verify VLANs

```bash
show vlan brief
```

---

## Test Connectivity

```bash
ping <destination-ip>
```

Example:

```bash
ping 10.0.0.63
```

---

# Task 4 — Verify Internet Connectivity

Test external connectivity by pinging:

```text
1.1.1.1
```

Routes toward the Internet:
- Have already been configured on R1
- Have already been configured on the Internet router

---

# Connectivity Flow

Traffic path:

```text
PC → SVI on SW2 → Default Route → R1 → Internet
```

---

# Operational Concepts Reinforced

| Concept | Purpose |
|---|---|
| Multilayer Switching | Layer 2 + Layer 3 functionality |
| Routed Ports | Point-to-point Layer 3 links |
| SVIs | VLAN default gateways |
| IP Routing | Enables routing on switches |
| Default Routes | Forward unknown traffic |
| Inter-VLAN Routing | Communication between VLANs |

---

# ROAS vs Multilayer Switching

| ROAS | Multilayer Switching |
|---|---|
| Uses router subinterfaces | Uses SVIs |
| Single physical router link | Local switch routing |
| Lower scalability | Higher scalability |
| Router bottleneck possible | Hardware-based routing |
| Common in small networks | Common in enterprise networks |

---

# Troubleshooting Mindset

When troubleshooting multilayer switching, verify:

- `ip routing` is enabled
- SVIs are up/up
- VLANs exist
- Access ports are assigned correctly
- Trunks are operational
- Routed interfaces have correct IPs
- Default routes are configured
- PCs have correct default gateways

Common problems usually involve:
- Missing VLANs
- Incorrect gateway addresses
- Disabled SVIs
- Missing default routes

---

# Key Takeaways

- Multilayer switches perform both switching and routing
- SVIs provide Layer 3 gateways for VLANs
- Routed ports behave like router interfaces
- `ip routing` enables Layer 3 functionality on switches
- Multilayer switching is more scalable than ROAS
- Default routes provide connectivity toward external networks
- Modern enterprise networks commonly use multilayer switching designs
