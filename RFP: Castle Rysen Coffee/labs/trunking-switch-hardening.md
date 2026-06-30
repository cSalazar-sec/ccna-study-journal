# VLAN Trunking and Switch Hardening

## Scenario

The Coffee House network already contains separate **Admin** and **Patron** VLANs, but the switching infrastructure still requires additional configuration before the environment is production-ready.

In this lab, I enabled the required trunk links between the router, switches, and wireless infrastructure, restored the Plex media server to the Admin VLAN, and hardened the access layer by shutting down every unused switchport.

This exercise reinforces several CCNA topics including VLAN management, 802.1Q trunking, interface security, and switch hardening best practices.

---

## Network Diagram

<img width="1403" height="1121" alt="image" src="https://github.com/user-attachments/assets/dac61c18-1acd-4890-b31a-be12abaef905" />

---

## VLAN Overview

| VLAN | Name | Purpose |
|------|------|---------|
| 10 | ADMIN | Administrative devices |
| 20 | PATRON | Customer wireless and guest devices |

---

## Lab Objectives

By completing this lab I practiced:

- Verifying VLAN assignments
- Configuring 802.1Q trunk links
- Restricting allowed VLANs on trunks
- Renaming VLANs
- Configuring access ports
- Securing unused switchports
- Verifying switch operation

---

# Task 0 – Verify the Existing VLAN Topology

Before making any changes, I verified the VLAN database and trunk status on both switches.

## Check VLAN Database

```bash
show vlan brief
```

Initially, VLANs already existed but still used their default names:

<img width="689" height="238" alt="image" src="https://github.com/user-attachments/assets/fe0738a3-c8c5-44d0-8745-dacb27049652" />

---

## Verify Trunk Status

<img width="220" height="41" alt="image" src="https://github.com/user-attachments/assets/c7636975-1dae-49ab-8a86-8792d369e0db" />


At the beginning of the lab, no interfaces were operating as trunks, so the command returned directly to the prompt.

---

# Task 1 – Configure the Trunk Links

To allow multiple VLANs to traverse the network, I configured the router-facing, inter-switch, and wireless uplinks as 802.1Q trunks.

---

## Enable the Router Interface

On **Cafe-RTR1**, the Router-on-a-Stick configuration already existed.

The parent interface simply needed to be enabled.

<img width="705" height="93" alt="image" src="https://github.com/user-attachments/assets/a2977871-9016-422a-ab18-9cd1ac1536b9" />

---

## Rename VLANs on Cafe-SW1

<img width="373" height="73" alt="image" src="https://github.com/user-attachments/assets/451492cb-4c1c-4415-887d-881ac0a95ebe" />

---

## Configure Trunk Interfaces on Cafe-SW1

Interfaces:

- Ethernet0/0 (Router)
- Ethernet0/1 (Switch)
- Ethernet1/0 (Wireless AP)

<img width="633" height="199" alt="image" src="https://github.com/user-attachments/assets/e2208003-5b4c-4223-995b-7472e82a447f" />

---

## Rename VLANs on Cafe-SW2

<img width="347" height="76" alt="image" src="https://github.com/user-attachments/assets/249c71e5-747a-4f8d-8e13-eaa7533157f0" />

---

## Configure Trunk Interfaces on Cafe-SW2

Interfaces:

- Ethernet0/1
- Ethernet1/0

<img width="639" height="165" alt="image" src="https://github.com/user-attachments/assets/7405d9b8-c7bd-4b3e-97c4-d677f66242cd" />


The wireless trunk may not immediately appear in the spanning-tree forwarding section until traffic begins flowing.

---

# Task 2 – Restore the Plex Server

The Plex media server belongs in the Admin VLAN.

I reconfigured its switchport as an access port in VLAN 10.

---

## Configure Ethernet6/0

```bash
interface ethernet6/0

description Cafe-PLEX1

switchport mode access
switchport access vlan 10
```


---

## Verify Connectivity

From the Plex server:

<img width="520" height="196" alt="image" src="https://github.com/user-attachments/assets/7b4f55b9-903e-47de-a8c1-9a8f686ca252" />

The first ping may fail while ARP populates.

Subsequent attempts should succeed.

---

# Task 3 – Secure Unused Ports

Unused switchports represent a physical security risk.

Following common enterprise best practices, I disabled every unused interface and clearly labeled each one.

---

## Cafe-SW1

<img width="683" height="543" alt="image" src="https://github.com/user-attachments/assets/a19d79b5-703d-4509-bb97-a7e2382172b8" />

 description UNUSED-LOCKDOWN

 shutdown

---

## Cafe-SW2

Repeated the same process, disabled every remaining interface and put them on an unsed VLAN.

---

Unused ports  remain assigned to VLAN 1(UNUSED) while administratively shut down.

---

# Verification Checklist

- [X] VLAN 10 renamed to **ADMIN-FLOOR**
- [X] VLAN 20 renamed to **PATRON-FLOOR**
- [X] Router interface enabled
- [X] Router trunk operational
- [X] Inter-switch trunk operational
- [X] Wireless AP trunk operational
- [X] Trunks allow only VLANs 10 and 20
- [X] Plex server moved to VLAN 10
- [X] Plex server successfully reached the default gateway
- [X] All unused interfaces disabled
- [X] Unused interfaces labeled **UNUSED-LOCKDOWN**
- [X] Configuration saved

---

# What I Practiced

- VLAN management
- 802.1Q trunk configuration
- Allowed VLAN lists
- Router-on-a-Stick integration
- Access port configuration
- Interface descriptions
- Switch hardening
- Layer 2 verification commands

---

# Key Takeaways

- Trunk links allow multiple VLANs to traverse a single physical connection using 802.1Q tags.
- Restricting the allowed VLAN list reduces unnecessary traffic and improves security.
- Meaningful VLAN names make network administration much easier than relying on default VLAN IDs.
- Access ports should belong to only one VLAN, while trunk ports carry traffic for multiple VLANs.
- Every unused switchport should be administratively shut down and clearly labeled to reduce the risk of unauthorized access.
- Verifying VLAN assignments and trunk status with `show vlan brief` and `show interfaces trunk` is an essential part of Layer 2 troubleshooting.
