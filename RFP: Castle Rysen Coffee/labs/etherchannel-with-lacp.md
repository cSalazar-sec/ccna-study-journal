# Configuring EtherChannel with LACP

## Overview

The Coffee House network currently relies on two redundant trunk links between **Cafe-SW01** and **Cafe-SW02**. While redundancy is available, **Rapid PVST** blocks one of the links to prevent Layer 2 loops, leaving half of the available bandwidth unused.

To improve both resiliency and throughput, these two physical links are combined into a single logical interface using **EtherChannel** with the **Link Aggregation Control Protocol (LACP)**.

After the EtherChannel is established, Spanning Tree no longer sees two independent links, it sees **one logical connection**, allowing both physical links to actively forward traffic.

---

## Lab Objectives

By the end of this lab you will be able to:

- Compare the Spanning Tree topology before and after EtherChannel.
- Configure an LACP-based EtherChannel between two Cisco switches.
- Verify the negotiation process and bundle status.
- Observe how Spanning Tree treats the Port-Channel as a single logical link.

---

## Network Diagram

<img width="1798" height="538" alt="image" src="https://github.com/user-attachments/assets/952a4e21-227a-4b53-ba0e-5a069c5d4c5d" />


---

# Task 1 — Examine the Existing Topology

### Objective

Review the current Spanning Tree state and identify which uplink is forwarding traffic and which has been placed into a blocking state.

### Steps

On **Cafe-SW01**:

1. Display the current spanning-tree topology.
2. Identify the uplinks toward **Cafe-SW02**.
3. Record which interface is currently blocked.

<img width="645" height="346" alt="image" src="https://github.com/user-attachments/assets/62ed49d1-7a5a-4b41-96ee-c57d4b31797b" />


Repeat the same verification on **Cafe-SW02**.

<img width="638" height="363" alt="image" src="https://github.com/user-attachments/assets/8eb76729-24c6-487c-87ab-28f06b0f1bb0" />

---

### What You Should Observe

Before EtherChannel is configured:

- One uplink forwards traffic.
- The second redundant uplink is blocked by STP.
- Only half of the available bandwidth is being utilized.

---

# Task 2 — Prepare the Physical Interfaces

### Objective

Before creating an EtherChannel, every member interface must have identical Layer 2 settings.

Any mismatch in configuration will prevent the bundle from forming.

### Important

Every interface participating in an EtherChannel should have matching:

- Switchport mode
- Native VLAN
- Allowed VLANs
- Speed
- Duplex

Cisco will refuse to bundle interfaces that do not match.

---

# Task 3 — Create the EtherChannel

### Objective

Use **LACP** to negotiate and establish a Port-Channel between both switches.

### Configure Cafe-SW01


<img width="829" height="88" alt="image" src="https://github.com/user-attachments/assets/5889e723-5853-42b8-a1c0-0cbed02eeb4c" />


### Configure Cafe-SW02

<img width="829" height="139" alt="image" src="https://github.com/user-attachments/assets/f8627085-9ae1-4917-a639-deb9dd25e84c" />

Both switches actively initiate LACP negotiation.

Once the negotiation completes, Cisco automatically creates:

```text
Port-channel1
```

---

## LACP Modes

| Mode | Description |
|------|-------------|
| **Active** | Actively negotiates an EtherChannel using LACP. |
| **Passive** | Waits for another device to initiate LACP. |

Two passive interfaces **will never form** an EtherChannel.

---

# Task 4 — Verify the EtherChannel

### Objective

Confirm the bundle formed successfully and that both physical interfaces joined the Port-Channel.

### Useful Commands

Display the EtherChannel summary:

<img width="638" height="362" alt="image" src="https://github.com/user-attachments/assets/70cf58ae-0f56-4593-bc38-fae04df02b5b" />

Display Port-Channel details:

<img width="591" height="508" alt="image" src="https://github.com/user-attachments/assets/8aa7c202-19bf-4d21-8157-a38187d65ad9" />

Verify the spanning-tree topology:

Cafe-SW01

<img width="643" height="332" alt="image" src="https://github.com/user-attachments/assets/ef44ebee-7ece-46cd-abb0-b230c895e5d2" />

Cafe-SW02

<img width="633" height="345" alt="image" src="https://github.com/user-attachments/assets/3cc237d7-bea9-4050-8e06-92dcc36dbf21" />

---

### Expected Results

A successful EtherChannel should display:

- Port-Channel1 is **Up**
- LACP protocol is in use
- Both member interfaces are bundled
- Trunk configuration is inherited by the Port-Channel

Spanning Tree should now reference:

```text
Port-channel1
```

instead of the individual Ethernet interfaces.

---

# Understanding the Difference

## Before EtherChannel

```
Cafe-SW01
   |\
   | \
   |  \
 FWD BLK
   |    \
Cafe-SW02
```

One link forwards traffic.

The second link is blocked by Spanning Tree.

Bandwidth available:

**1 Link**

---

## After EtherChannel

```
Cafe-SW01

      ||
===================
   Port-Channel1
===================

      ||

Cafe-SW02
```

Both physical links now function as **one logical interface**.

Traffic is distributed across both links while maintaining loop-free Layer 2 connectivity.

---

# Verification Checklist

- [X] Both switches use LACP.
- [X] Port-Channel1 is operational.
- [X] Member interfaces appear in the EtherChannel bundle.
- [X] Spanning Tree references Port-Channel1 instead of individual links.
- [X] Traffic successfully passes across the EtherChannel.

---

# Key Takeaways

- EtherChannel combines multiple physical interfaces into one logical link.
- LACP (IEEE 802.3ad / 802.1AX) dynamically negotiates the bundle between switches.
- Every member interface must share identical Layer 2 characteristics.
- Spanning Tree treats the Port-Channel as a single interface, allowing all bundled links to actively forward traffic.
- EtherChannel increases available bandwidth, improves resiliency, and eliminates idle redundant links that would otherwise be blocked by STP.
