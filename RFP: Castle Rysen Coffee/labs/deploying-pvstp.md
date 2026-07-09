# Rapid PVST Root Bridge Election and Layer 2 Hardening

## Scenario

The Coffee House and Fallout networks have grown into a multi-switch environment with redundant Layer 2 paths between switches. Without Spanning Tree, these redundant links could create switching loops, resulting in broadcast storms and unstable network behavior.

In this lab, I migrated all switches to **Rapid PVST (Rapid Per-VLAN Spanning Tree)**, elected deterministic root bridges for each site, configured the trunk links connecting the switching infrastructure, and hardened unused access ports using **PortFast** and **BPDU Guard**.

---

## Network Diagram

<img width="1150" height="850" alt="image" src="https://github.com/user-attachments/assets/713d643f-1bd8-4e2e-b6c0-8bc28fb98495" />

---

## Lab Objectives

By completing this lab I practiced:

- Migrating switches to Rapid PVST
- Creating VLANs across multiple switches
- Electing root and secondary root bridges
- Configuring trunk links
- Verifying STP topology
- Understanding blocked redundant links
- Configuring PortFast
- Configuring BPDU Guard

---

# Spanning Tree Design

## Coffee House

| Switch | Role |
|---------|------|
| Cafe-SW1 | Root Bridge |
| Cafe-SW2 | Secondary Root |

---

## Fallout Shelter

| Switch | Role |
|---------|------|
| Fallout-SW1 | Root Bridge |
| Fallout-SW2 | Secondary Root |
| Fallout-SW3 | Access Switch |

---

# Task 0 – Enable Rapid PVST

Cisco switches default to PVST+, but this lab requires **Rapid PVST**, which provides significantly faster convergence after topology changes.

---

## Cafe Switches

There were no VLANs in neither of the switches so I created them.

<img width="665" height="182" alt="image" src="https://github.com/user-attachments/assets/56e2cef1-6a00-406a-8b47-0c86b6dfc868" />

```bash
vlan 10
vlan 20
```

Enable Rapid PVST.

```bash
spanning-tree mode rapid-pvst
```

Repeat on:

- Cafe-SW1
- Cafe-SW2

---

## Fallout Switches

Create the required VLANs.

```bash
vlan 10
vlan 20
vlan 30
vlan 40
```

Enable Rapid PVST.

```bash
spanning-tree mode rapid-pvst
```

Repeat on:

- Fallout-SW1
- Fallout-SW2
- Fallout-SW3

---

## Verify STP Mode

```bash
show spanning-tree summary
```

Expected:

<img width="451" height="220" alt="image" src="https://github.com/user-attachments/assets/2fe12bd7-fc72-44b0-a766-475c8bf47cab" />

---

# Task 1 – Elect the Root Bridges

Rather than allowing STP to elect a root bridge based on the lowest MAC address, I manually selected the switches that should control each Layer 2 domain.

This creates a predictable and stable spanning-tree topology.

---

## Cafe-SW1

Configure as the primary root bridge.

```bash
spanning-tree vlan 1,10,20 root primary
```

---

## Cafe-SW2

Configure as the backup root bridge.

```bash
spanning-tree vlan 1,10,20 root secondary
```

---

## Fallout-SW1

Assign a bridge priority of **4096**.

```bash
spanning-tree vlan 1,10,20,30,40 priority 4096
```

---

## Fallout-SW2

Assign a bridge priority of **8192**.

```bash
spanning-tree vlan 1,10,20,30,40 priority 8192
```

---

## Verify Root Bridge Election

```bash
show spanning-tree
```

On each switch, verify:

- Root Bridge
- Root Port
- Bridge Priority
- Port Roles

---

# Understanding Root Bridge Election

Spanning Tree elects the switch with the **lowest Bridge ID** as the root bridge.

The Bridge ID consists of:

- Bridge Priority
- MAC Address

Lower priority values take precedence.

Example:

```text
4096

↓

8192

↓

32768 (default)
```

Because the priorities were manually configured, the desired switches consistently become the root bridges.

---

# Task 2 – Configure Trunk Links

Switch-to-switch connections must carry multiple VLANs.

I configured each inter-switch link as an 802.1Q trunk.

---

## Cafe Inter-Switch Trunk Cafe-SW1 & Cafe-SW2

Allowed VLANs:

- VLAN 1
- VLAN 10
- VLAN 20

<img width="381" height="89" alt="image" src="https://github.com/user-attachments/assets/c1f50dcf-fc54-4a3c-9faf-855990aaae97" />


---

## Cafe-to-Fallout Trunk Cafe-SW1 & Cafe-SW2

Allowed VLANs:

- VLAN 1
- VLAN 10
- VLAN 20
- VLAN 30
- VLAN 40

<img width="399" height="88" alt="image" src="https://github.com/user-attachments/assets/f05ae4a7-f5db-4fcb-8845-6e1a4b0da2df" />

---

## Fallout Core Trunks Fallout-SW1 & Fallout-SW2

Allowed VLANs:

- VLAN 1
- VLAN 10
- VLAN 20
- VLAN 30
- VLAN 40

<img width="415" height="450" alt="image" src="https://github.com/user-attachments/assets/ce640e45-a770-4f5f-8615-aa1a94120241" />

---

## Verify Trunk Status

```bash
show interfaces trunk
```

Verify:

- Interfaces are trunking
- Allowed VLANs are correct
- Active VLANs are correct

---

## Verify Spanning Tree

```bash
show spanning-tree
```

One redundant uplink should be forwarding while the alternate path is blocked.

<img width="611" height="456" alt="image" src="https://github.com/user-attachments/assets/f3468798-02b4-4a2d-9c33-d793fcb29e25" />

Typical port roles:

```text
Root

Designated

Alternate
```

This confirms STP has successfully prevented a Layer 2 loop while preserving redundancy.

---

# Task 3 – Harden Access Ports

Access ports connected to end devices should transition immediately to forwarding and be protected against accidental switch connections.

---

## Configure PortFast

Example:

```bash
interface ethernet0/3

 spanning-tree portfast
```
Configured switchport mode access, portfast and bpduguard

<img width="330" height="433" alt="image" src="https://github.com/user-attachments/assets/06a69d0a-73aa-4996-9b5a-307f54311409" />

Repeat on unused or endpoint-facing interfaces, including:

### Cafe

- Ethernet0/3
- Ethernet1/0
- Ethernet1/1
- Ethernet1/2
- Ethernet1/3

### Fallout Switches

- Ethernet0/0
- Unused Ethernet1/x interfaces

---

## Enable BPDU Guard

Configure globally:

```bash
spanning-tree portfast bpduguard default
```

Every PortFast-enabled interface is now automatically protected.

---

## Verify PortFast

```bash
show spanning-tree interface ethernet0/3 detail
```

Expected:

<img width="552" height="219" alt="image" src="https://github.com/user-attachments/assets/50935c07-644b-41d1-a6f5-75473727f9f4" />

---

## Verify BPDU Guard

```bash
show spanning-tree interface ethernet0/3 detail
```

<img width="571" height="216" alt="image" src="https://github.com/user-attachments/assets/27857472-fc58-472b-8fd4-0d2364c1994f" />


The output should indicate BPDU Guard is enabled on PortFast interfaces.

This lab contains only switch infrastructure, so no rogue device is available to trigger an err-disabled event.

Verification is performed by inspecting the configuration.

---

# Verification Checklist

- [X] Rapid PVST enabled on all switches
- [X] Required VLANs created
- [X] Cafe-SW1 elected root bridge
- [X] Cafe-SW2 elected secondary root
- [X] Fallout-SW1 configured with priority 4096
- [X] Fallout-SW2 configured with priority 8192
- [X] Trunk links configured
- [X] Allowed VLAN lists verified
- [X] Redundant path correctly blocked by STP
- [X] PortFast configured on access ports
- [X] BPDU Guard enabled
- [X] Configurations saved

---

# What I Practiced

- Rapid PVST configuration
- VLAN deployment
- Root bridge election
- Bridge priority planning
- 802.1Q trunking
- Layer 2 redundancy
- STP verification
- PortFast
- BPDU Guard
- Enterprise switch hardening

---

# Key Takeaways

- Rapid PVST provides faster convergence than traditional PVST while maintaining a separate spanning-tree instance for each VLAN.
- Root bridge placement should always be planned rather than left to the default bridge election process.
- Assigning primary and secondary root bridges creates a predictable and stable Layer 2 topology.
- Trunk links must carry only the VLANs required across each connection to reduce unnecessary broadcast traffic.
- Redundant Layer 2 links improve availability, while Spanning Tree ensures that only one forwarding path is active at a time to prevent loops.
- PortFast should be enabled only on endpoint-facing interfaces to reduce connection delays.
- BPDU Guard protects PortFast interfaces by automatically disabling a port if it receives a BPDU, helping prevent accidental Layer 2 loops caused by unauthorized switches.
