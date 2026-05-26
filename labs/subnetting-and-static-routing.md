# Subnetting and Static Routing Lab

---

# Lab Overview

This lab focuses on:
- IPv4 subnetting using VLSM
- IP address planning
- Router interface addressing
- Static routing configuration
- End-to-end connectivity verification

The goal is to subnet the `192.168.5.0/24` network efficiently and configure routing so all LANs can communicate successfully.

---

# Lab Objectives

- Subnet a `/24` network using VLSM
- Allocate subnets based on host requirements
- Assign IP addresses to endpoints and router interfaces
- Configure static routes between routers
- Verify end-to-end connectivity
- Practice route selection and routing-table logic

---

# Network Requirements

| Network | Required Hosts |
|---|---|
| LAN 1 | 45 Hosts |
| LAN 2 | 64 Hosts |
| LAN 3 | 14 Hosts |
| LAN 4 | 9 Hosts |
| Point-to-Point Link | 2 Hosts |

---

# Addressing Rules

The following addressing conventions were used throughout the lab:

- First usable IP address → Assigned to PCs/end devices
- Last usable IP address → Assigned to router interfaces
- Static routes configured for full connectivity between LANs

---

# VLSM Addressing Plan

---

# LAN 1

| Field | Value |
|---|---|
| Network Address | 192.168.5.128/26 |
| Subnet Mask | 255.255.255.192 |
| Usable Range | 192.168.5.129 - 192.168.5.190 |
| Broadcast Address | 192.168.5.191 |
| Host Requirement | 45 Hosts |

---

# LAN 2

| Field | Value |
|---|---|
| Network Address | 192.168.5.0/25 |
| Subnet Mask | 255.255.255.128 |
| Usable Range | 192.168.5.1 - 192.168.5.126 |
| Broadcast Address | 192.168.5.127 |
| Host Requirement | 64 Hosts |

---

# LAN 3

| Field | Value |
|---|---|
| Network Address | 192.168.5.192/28 |
| Subnet Mask | 255.255.255.240 |
| Usable Range | 192.168.5.193 - 192.168.5.206 |
| Broadcast Address | 192.168.5.207 |
| Host Requirement | 14 Hosts |

---

# LAN 4

| Field | Value |
|---|---|
| Network Address | 192.168.5.208/28 |
| Subnet Mask | 255.255.255.240 |
| Usable Range | 192.168.5.209 - 192.168.5.222 |
| Broadcast Address | 192.168.5.223 |
| Host Requirement | 9 Hosts |

---

# Point-to-Point Link (R1 ↔ R2)

| Field | Value |
|---|---|
| Network Address | 192.168.5.224/30 |
| Subnet Mask | 255.255.255.252 |
| Usable Range | 192.168.5.225 - 192.168.5.226 |
| Broadcast Address | 192.168.5.227 |
| Host Requirement | 2 Hosts |

---

# Network Diagram

<img width="925" height="651" alt="image" src="https://github.com/user-attachments/assets/5aeee984-b877-4b4e-af5b-eb81fd272397" />

---

# IP Address Assignment

## Endpoint Addressing

The:

```text
First usable IP address
```
was assigned to the endpoint devices in each LAN.

<img width="441" height="151" alt="image" src="https://github.com/user-attachments/assets/4b2c2304-0544-4d0f-9ebb-89e178e6de8a" />

Example:

| Network | Endpoint IP |
|---|---|
| LAN 1 | 192.168.5.129 |
| LAN 2 | 192.168.5.1 |
| LAN 3 | 192.168.5.193 |
| LAN 4 | 192.168.5.209 |

---

## Router Interface Addressing

The:

```text
Last usable IP address
```
was assigned to the router interface in each LAN.

<img width="566" height="58" alt="image" src="https://github.com/user-attachments/assets/df2b42cc-6d46-43ef-bf68-cba1cb62705f" />

Example:

| Network | Router Interface IP |
|---|---|
| LAN 1 | 192.168.5.190 |
| LAN 2 | 192.168.5.126 |
| LAN 3 | 192.168.5.206 |
| LAN 4 | 192.168.5.222 |

---

# Static Routing Configuration

Static routes were configured to allow:
- Inter-LAN communication
- End-to-end reachability
- Full connectivity between all PCs

---

# Static Route Methods Used

This lab includes static routes configured using:

| Method | Description |
|---|---|
| Exit Interface | Specifies the outgoing interface |
| Next-Hop IP | Specifies the next router IP address |

R1 Exit Interface - An interesting point is that, when you specify only the exit interface of the static route, the system will advise that the DESTINATION network is DIRECTLY CONNECTED, even though, that network is not directly connected.

<img width="495" height="54" alt="image" src="https://github.com/user-attachments/assets/6f1dabf7-0490-4644-9142-86af8cee7c2e" />

R2 Next-Hop

<img width="440" height="54" alt="image" src="https://github.com/user-attachments/assets/db36155c-bcbd-4955-bef3-b0df7418bacf" />

---

# Example Static Routes

## Using Next-Hop

```bash
ip route 192.168.5.0 255.255.255.128 192.168.5.225
```

---

## Using Exit Interface

```bash
ip route 192.168.5.192 255.255.255.240 g0/0/0
```

---

## Using Both

```bash
ip route 192.168.5.0 255.255.255.128 g0/0/0 192.168.5.225
```

---

# Verification Commands

## View Routing Table

```bash
show ip route
```

---

## Verify Interface Status

```bash
show ip interface brief
```

---

## Test Connectivity

```bash
ping <destination-ip>
```

Example:

```bash
ping 192.168.5.193
```

Ping made from PC2 in LAN 2, to PC3 in LAN3.

<img width="449" height="152" alt="image" src="https://github.com/user-attachments/assets/882cefe8-a30e-4869-bbaf-16e5b0341dc3" />

<img width="419" height="175" alt="image" src="https://github.com/user-attachments/assets/786d6e87-033b-49e3-a886-0b6e35bea702" />


---

# Routing Behavior Observed

During packet forwarding:

- Routers examined destination IP addresses
- Routing tables were consulted
- The most specific matching route was selected
- Packets were forwarded toward the correct next-hop

This lab reinforced:
- Longest prefix match
- Static route logic
- Default gateway behavior
- Route selection principles

---

# Key Takeaways

- VLSM allows efficient address utilization
- Different subnet sizes support different host requirements
- Static routes enable communication between remote networks
- Routers require routes to forward traffic
- Longest prefix match determines route selection
- Both next-hop and exit-interface static routes are valid configuration methods
- Proper IP planning simplifies troubleshooting and scalability
