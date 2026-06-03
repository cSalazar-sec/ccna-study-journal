# DTP and VTP Lab

---

# Lab Overview

- Dynamic Trunking Protocol (DTP)
- VLAN Trunking Protocol (VTP)

The goal is to understand how switches:
- Establish trunk links
- Exchange VLAN information
- Manage VLAN databases
- Control VLAN propagation across the network

The lab also reinforces VLAN assignment and access port configuration best practices.

---

# Lab Objectives

- Configure manual trunk links
- Disable DTP on trunk interfaces
- Verify trunk operational status
- Configure VTP domains
- Understand VTP Server, Client, and Transparent modes
- Observe VLAN propagation behavior
- Configure access ports manually
- Verify DTP behavior on access ports

---

# Network Diagram

<img width="1612" height="976" alt="image" src="https://github.com/user-attachments/assets/ddb2b621-6b91-4c58-97ac-d43768deb572" />

---

# Technologies Covered

| Technology | Purpose |
|------------|---------|
| VLANs | Layer 2 segmentation |
| Trunking | Carry multiple VLANs over one link |
| DTP | Dynamic trunk negotiation |
| VTP | VLAN database synchronization |
| Access Ports | Connect end devices to VLANs |

---

# What is DTP?

```text
DTP = Dynamic Trunking Protocol
```

DTP is a Cisco proprietary protocol that allows neighboring switches to negotiate whether a link should operate as:

- Access
- Trunk

Without manual configuration.

---

# Why Disable DTP?

In production environments, best practice is:

```text
Manually configure trunk ports and disable DTP.
```

Benefits:

- Improved security
- Predictable behavior
- Reduced unnecessary negotiation traffic
- Easier troubleshooting

---

# What is VTP?

```text
VTP = VLAN Trunking Protocol
```

VTP allows switches to:

- Share VLAN information
- Synchronize VLAN databases
- Reduce manual VLAN creation

within a common VTP domain.

---

# VTP Modes

---

## Server Mode

A VTP Server can:

- Create VLANs
- Modify VLANs
- Delete VLANs
- Advertise VLAN information

Changes made on the server propagate throughout the VTP domain.

---

## Client Mode

A VTP Client:

- Receives VLAN updates
- Cannot create VLANs
- Cannot modify VLANs
- Cannot delete VLANs

The VLAN database is learned from VTP servers.

---

## Transparent Mode

A VTP Transparent switch:

- Maintains its own VLAN database
- Does not synchronize VLANs from VTP
- Forwards VTP advertisements

Changes remain local to the switch.

---

# Task 1 — Configure Trunk Ports

## Objective

Configure all switch-to-switch links as trunks.

Disable DTP on those interfaces.

---

# Configuration Requirements

- Configure trunk mode manually
- Disable DTP negotiation
- Verify operational status

---

## Example Configuration

```bash
interface g0/1
 switchport mode trunk
 switchport nonegotiate
```
<img width="270" height="41" alt="image" src="https://github.com/user-attachments/assets/33d1583f-0368-49ba-9429-138ca8f928bd" />

---

# Verify Negotiation of Trunking Status

```bash
show interface g0/1 switchport
```
<img width="346" height="143" alt="image" src="https://github.com/user-attachments/assets/7f0153bf-98cf-40d5-9725-58389463e1eb" />

---


# Task 2 — Configure SW1 as VTP Server

## Objective

Configure SW1 as the VTP Server for the CCNA domain.

---

# Configuration Requirements

Configure:

```text
VTP Domain: CCNA
```

Create:

- VLAN 10
- VLAN 20
- VLAN 30

---

## Example Configuration

```bash
vtp domain CCNA
vtp mode server - Switches have configured "VTP Operating Mode: Server" by default
vlan 10
vlan 20
vlan 30

Revision was increased by 1, per each vlan we created
```
<img width="538" height="236" alt="image" src="https://github.com/user-attachments/assets/ed9f612a-d200-493e-b366-1da341ca2a93" />

---

# Verification

```bash
show vlan brief
```

---

# VTP Verification

```bash
show vtp status
```

---

# Investigation Question

After VLAN creation:

```text
Have VLANs 10, 20, and 30 appeared on SW2 and SW3?
```

Expected Result:

- Yes, assuming:
  - Same VTP domain
  - Trunk connectivity exists
  - Revision numbers permit synchronization

<img width="579" height="337" alt="image" src="https://github.com/user-attachments/assets/31775fc6-7cee-4474-b062-e43a205708ca" />

---

# Task 3 — Configure SW2 as VTP Transparent

## Objective

Configure SW2 in:

```text
VTP Transparent Mode
```

---

## Configuration

```bash
vtp mode transparent
```

---

# Create VLAN40

```bash
vlan 40
 name VLAN40
```

---

# Investigation Question

```text
Does VLAN40 appear on SW1 or SW3?
```

Expected Result:

```text
No, switches in VTP transparent mode don't sent VTP advertisements
```
<img width="582" height="236" alt="image" src="https://github.com/user-attachments/assets/0713f957-f868-4ac1-b8ac-447ab4cd5307" />

Why?

Because:

- Transparent mode stores VLAN changes locally
- VLAN40 is not added to the VTP database of other switches

---

# Task 4 — Configure SW3 as VTP Client

## Objective

Configure SW3 in:

```text
VTP Client Mode
```

---

## Configuration

```bash
vtp mode client
```

---

# Attempt to Create VLAN50

```bash
vlan 50
```

---

# Investigation Question

```text
Is VLAN50 created successfully?
```

Expected Result:

```text
No
```
<img width="471" height="29" alt="image" src="https://github.com/user-attachments/assets/4cc3988e-1a64-4c6c-9252-37fe913d8ef1" />

Why?

Because:

- VTP Clients cannot create VLANs
- VLAN databases are controlled by VTP Servers

---

# Task 5 — Configure Access Ports

## Objective

Assign host-facing interfaces to the correct VLANs.

Configure them manually as access ports.

---

# Example Configuration

```bash
interface f0/1
 switchport mode access
 switchport access vlan 10
```

---

# Verify VLAN Assignment

```bash
show vlan brief
```

---

# Verify Switchport Status

```bash
show interfaces switchport
```

---

# Investigation Question

```text
Is DTP still enabled on access ports?
```

Expected Result:

```text
No, the switchport mode command disables DTP
```

<img width="392" height="236" alt="image" src="https://github.com/user-attachments/assets/ff53ecf9-a422-4c3d-9df9-8d2573d1b903" />

To disable DTP entirely:

```bash
switchport nonegotiate
```

However:

```text
switchport nonegotiate
```

is most commonly used on trunk links.

---

# Verification Commands

## Verify VLAN Database

```bash
show vlan brief
```

---

## Verify VTP Information

```bash
show vtp status
```

---

## Verify Trunk Links

```bash
show interfaces trunk
```

---

## Verify Switchport Status

```bash
show interfaces switchport
```

---

# Operational Concepts Reinforced

| Concept | Purpose |
|----------|---------|
| DTP | Dynamic trunk negotiation |
| Trunks | Carry multiple VLANs |
| VTP Server | Creates and advertises VLANs |
| VTP Client | Receives VLANs |
| VTP Transparent | Maintains local VLAN database |
| Access Ports | Connect end devices |
| VLAN Database | Stores VLAN information |

---

# Troubleshooting Mindset

When troubleshooting DTP or VTP issues, verify:

- VTP domain names match
- Trunk links are operational
- VLANs exist on the VTP server
- VTP modes are configured correctly
- Revision numbers are correct
- DTP negotiation status matches expectations
- Allowed VLAN lists include required VLANs

Most VTP issues come down to:
- Domain mismatches
- Incorrect VTP modes
- Missing trunk connectivity

---

# Key Takeaways

- DTP dynamically negotiates trunk links
- Best practice is to manually configure trunks and disable DTP
- VTP distributes VLAN information across switches
- VTP Servers control the VLAN database
- VTP Clients cannot create VLANs
- VTP Transparent switches maintain independent VLAN databases
- Trunk links are required for VLAN propagation
- Understanding VTP modes is critical when managing large switched networks
