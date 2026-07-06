# Rapid PVST, PortFast, and BPDU Guard

## Scenario

The Coffee House network uses **Rapid PVST (Rapid Per-VLAN Spanning Tree)** to prevent Layer 2 loops while maintaining redundancy.

Although Rapid PVST converges much faster than traditional STP, end devices still benefit from **PortFast**, which allows access ports to begin forwarding traffic immediately instead of progressing through the normal spanning-tree states.

To protect the network from accidental loops caused by someone plugging in an unmanaged or rogue switch, I also enabled **BPDU Guard**. Any PortFast-enabled interface that receives a BPDU is immediately placed into an **err-disabled** state, protecting the Layer 2 topology.

---

## Network Diagram

<img width="1000" height="850" alt="image" src="https://github.com/user-attachments/assets/53c06f96-6f93-4e34-9705-be9c1b1b7797" />


---

## Topology

| Device | Interface | Connected To |
|---------|-----------|--------------|
| Bunker-SW1 | Ethernet0/0 | Shelter-Core (Trunk) |
| Bunker-SW1 | Ethernet0/3 | Bunker-Host |
| Bunker-SW1 | Ethernet1/0 | Rogue-SW Ethernet0/0 |

---

## Lab Objectives

By completing this lab I practiced:

- Inspecting Rapid PVST operation
- Understanding STP port states
- Configuring PortFast
- Configuring BPDU Guard
- Triggering an err-disabled port
- Recovering from BPDU Guard violations
- Verifying spanning-tree behavior

---

# Task 0 – Inspect Default Rapid PVST Behavior

Before enabling PortFast, I observed how a normal access port behaves when a device reconnects.

---

## Verify Spanning Tree Mode

<img width="575" height="328" alt="image" src="https://github.com/user-attachments/assets/b196e58d-36ce-4711-8e49-b96c5da99cd6" />

---

## Inspect the Access Port

<img width="534" height="174" alt="image" src="https://github.com/user-attachments/assets/fda30c65-0087-4602-a2d5-f73017bbea3f" />

This shows the current spanning-tree role, state, timers, and PortFast status.

---

## Simulate an Endpoint Reconnection

To observe Rapid PVST convergence, I reset the interface.

```bash
interface ethernet0/3
 shutdown
 no shutdown
```

Immediately afterward, I checked the interface again.

<img width="521" height="340" alt="image" src="https://github.com/user-attachments/assets/2692236a-9360-4776-9889-cdc7350333a3" />

<img width="519" height="168" alt="image" src="https://github.com/user-attachments/assets/cc2a1cc4-1d18-4a03-a28b-fd93a0f04c6c" />


Depending on timing, the interface may briefly appear in the **Learning** state before transitioning to **Forwarding**, or it may already be forwarding by the time the command is issued.

---

# Understanding Rapid PVST

Rapid PVST converges significantly faster than classic STP.

Typical transition:

```text
Blocking

↓

Learning

↓

Forwarding
```

Although convergence is quick, end devices still experience a short delay before traffic begins flowing.

---

# Task 1 – Configure PortFast

Since hosts cannot create Layer 2 loops on their own, access ports can safely skip the normal spanning-tree convergence process.

I enabled PortFast only on endpoint-facing interfaces.

The trunk uplink remained a standard spanning-tree port.

---

## Configure PortFast

<img width="431" height="39" alt="image" src="https://github.com/user-attachments/assets/d22dce1d-e396-4437-97fb-1dddb6b1b8f7" />


**Do not configure PortFast on the trunk interface:**

```text
Ethernet0/0
```

---

## Verify PortFast

```bash
show spanning-tree interface ethernet0/3 detail
```

Expected output includes:

<img width="558" height="188" alt="image" src="https://github.com/user-attachments/assets/09d1b69d-189b-4430-83a1-4f0c30eaa3e0" />


---

## Test PortFast

Reset the interface once more.

```bash
interface ethernet0/3
 shutdown
 no shutdown
```

The port should transition directly into the forwarding state without waiting through the normal convergence process.

---

# Understanding PortFast

Without PortFast:

```text
Blocking

↓

Learning

↓

Forwarding
```

With PortFast:

```text
Forwarding
```

This significantly reduces the time required for end devices to regain network connectivity after reconnecting.

---

# Task 2 – Configure BPDU Guard

PortFast assumes that only end devices connect to the interface.

If another switch is accidentally connected, it begins sending **Bridge Protocol Data Units (BPDUs)**.

Receiving BPDUs on a PortFast interface indicates a potential Layer 2 loop.

BPDU Guard immediately disables the interface to protect the network.

---

## Enable BPDU Guard

Configure BPDU Guard globally.

<img width="475" height="17" alt="image" src="https://github.com/user-attachments/assets/6222b723-a502-42ed-9e4c-4c44645ea1ab" />


This automatically protects every PortFast-enabled interface.

---

## Trigger a BPDU Guard Violation

On **Rogue-SW**, reset the connected interface.

```bash
interface ethernet0/0
 shutdown
 no shutdown
```

The rogue switch immediately begins transmitting BPDUs.

---

## Verify the Err-Disabled State

On **Bunker-SW1**:

```bash
show interfaces status
```

Expected:

```text
Ethernet1/0

err-disabled
```

---

## Verify the Log Message

```bash
show logging
```

Example:

```text
%SPANTREE-2-BLOCK_BPDUGUARD

Received BPDU on PortFast-enabled interface.

Disabling interface Ethernet1/0
```

---

# Recover the Interface

First, stop the BPDU source.

On **Rogue-SW**:

```bash
interface ethernet0/0
 shutdown
```

---

Then restore the interface on **Bunker-SW1**.

```bash
interface ethernet1/0
 shutdown
 no shutdown
```

Verify:

```bash
show interfaces status
```

Expected:

```text
connected
```

---

# Verification Commands

## Check Spanning Tree

```bash
show spanning-tree
```

---

## Verify PortFast

```bash
show spanning-tree interface ethernet0/3 detail
```

---

## Check Interface Status

```bash
show interfaces status
```

---

## View Log Messages

```bash
show logging
```

---

# Save Configuration

```bash
copy running-config startup-config
```

---

# Verification Checklist

- [X] Rapid PVST verified
- [X] Default spanning-tree behavior observed
- [X] PortFast enabled on Ethernet0/3
- [X] PortFast enabled on Ethernet1/0
- [X] Trunk interface left unchanged
- [X] PortFast operation verified
- [X] BPDU Guard enabled globally
- [X] Rogue switch triggered BPDU Guard
- [X] Ethernet1/0 entered err-disabled
- [X] Recovery procedure completed
- [X] Configuration saved

---

# What I Practiced

- Rapid PVST verification
- PortFast configuration
- BPDU Guard configuration
- Err-disabled troubleshooting
- STP verification commands
- Layer 2 loop prevention
- Interface recovery procedures

---

# Key Takeaways

- Rapid PVST provides much faster convergence than traditional STP, but access ports may still briefly pass through transitional states.
- PortFast allows endpoint-facing interfaces to begin forwarding traffic immediately, reducing connection delays for users.
- PortFast should only be enabled on interfaces connected to end devices, never on switch-to-switch trunk links.
- BPDU Guard complements PortFast by automatically disabling any PortFast interface that receives a BPDU, preventing accidental Layer 2 loops caused by rogue switches.
- An interface placed into an **err-disabled** state must be recovered only after the BPDU source has been removed.
- Useful verification commands include `show spanning-tree`, `show spanning-tree interface detail`, `show interfaces status`, and `show logging`.
