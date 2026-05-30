# Switch-Based Device Discovery and Topology Mapping Lab

---

# Lab Overview

This lab focuses on discovering and documenting a network using only switch-level visibility.

The objective is to identify:

* Physical connectivity between switches
* VLAN usage across the network
* Endpoint locations
* MAC-to-port mappings
* Management connectivity

The information gathered will be used to build an accurate topology map before network automation is deployed.

---

# Scenario

Two switches and three critical workstations are currently online.

Before automation can be introduced, the senior engineer requires a verified network map that identifies:

* Switch-to-switch connectivity
* VLAN assignments
* Endpoint locations
* Interface documentation
* Management access information

Your task is to perform discovery using only switch access and produce operational documentation for the engineering team.

---

# Training Objectives

* Discover trunk links and VLAN assignments
* Trace devices using ARP and MAC address tables
* Identify endpoint switchports
* Verify switch management connectivity
* Document network topology
* Apply interface descriptions
* Validate network continuity after changes

---

# Management IP Addresses

| Device     | Management IP   |
| ---------- | --------------- |
| RW-CORE-SW | 192.168.42.1/24 |
| RW-ACC-SW  | 192.168.42.2/24 |

---

# Network Diagram

<img width="1686" height="933" alt="image" src="https://github.com/user-attachments/assets/348dc56b-a743-4f9e-aec5-3895ba4d17cf" />

---

# Task 1 — Establish the Core View

## Objective

Build a baseline understanding of the core switch and identify how the access layer connects into the network.

---

## Requirements

* Log into RW-CORE-SW
* Verify the hostname
* Identify the uplink toward RW-ACC-SW
* Verify trunk status
* Document VLAN usage
* Apply interface descriptions
* Record management information

---

## Useful Commands

### Verify Hostname

```bash
show running-config | include hostname
```

---

### Discover Neighboring Devices

```bash
show cdp neighbors
```

---

### Verify Trunk Interfaces

```bash
show interfaces trunk
```

---

### View Interface Status

```bash
show interfaces status
```

---

### Verify Management Configuration

```bash
show ip interface brief
```

---

## Documentation Items

Record:

* Core hostname
  
<img width="168" height="20" alt="image" src="https://github.com/user-attachments/assets/87ac0bf3-a5e7-4daa-8781-dee50d565c59" />

* Uplink interface

<img width="664" height="129" alt="image" src="https://github.com/user-attachments/assets/e7281f7f-3387-41f7-a728-e3e8fd3f7aec" />

* Trunk VLANs
  
<img width="325" height="71" alt="image" src="https://github.com/user-attachments/assets/2dccb90d-4007-4801-947c-f2b5fbf50912" />

* Management IP
  
<img width="708" height="127" alt="image" src="https://github.com/user-attachments/assets/09decfd5-b871-40a4-9a19-b04fbf660393" />

* Interface descriptions
  
<img width="676" height="130" alt="image" src="https://github.com/user-attachments/assets/9313d621-eedc-48c5-9500-59d30fe3d233" />

---

# Task 2 — Discover the Access Layer

## Objective

Identify how RW-ACC-SW connects endpoints and verify uplink connectivity.

---

## Requirements

* Verify hostname

  <img width="192" height="20" alt="image" src="https://github.com/user-attachments/assets/e044d275-d0ec-4f26-8184-59f47de96df1" />

* Locate InventoryStation & ManagerConsole

<img width="605" height="309" alt="image" src="https://github.com/user-attachments/assets/a9b83e6b-fa24-4969-a3a7-9c89a6e99859" />

* Identify uplink to RW-CORE-SW

<img width="660" height="131" alt="image" src="https://github.com/user-attachments/assets/648061ce-fe67-4f53-a119-0f8baf041fa2" />

* Verify trunk status

<img width="623" height="83" alt="image" src="https://github.com/user-attachments/assets/ec31e9a5-6486-4519-b028-61b43ba8667e" />

* Record management information

<img width="692" height="131" alt="image" src="https://github.com/user-attachments/assets/f338721f-bcb8-4f05-a097-d21c66e125bb" />

* Add interface descriptions
  
<img width="681" height="127" alt="image" src="https://github.com/user-attachments/assets/64ef0fde-9c39-4503-8c80-3ef171cafdfa" />

---

## Useful Commands

### View MAC Address Table

```bash
show mac address-table
```

---

### View Neighbor Information

```bash
show cdp neighbors
```

---

### Verify Trunk Status

```bash
show interfaces trunk
```

---

### Verify Interface Status

```bash
show ip interface brief
```

---

# Task 3 — Trace Each Endpoint

## Objective

Convert endpoint IP addresses into MAC-to-port mappings.

---

# Endpoint List

| Device           | IP Address    |
| ---------------- | ------------- |
| BaristaPOS       | 192.168.42.37 |
| InventoryStation | 192.168.42.38 |
| ManagerConsole   | 192.168.42.39 |

---

## Discovery Process

### Step 1 — Refresh ARP Information

Generate traffic if necessary:

```bash
ping <ip-address>
```

---

### Step 2 — Locate MAC Address

```bash
show arp
```

---

### Step 3 — Locate Switchport

```bash
show mac address-table
```

---

### Step 4 — Follow Uplinks

If the MAC address is learned through a trunk:

```bash
show cdp neighbors
```

Move to the neighboring switch and repeat.

---

## Endpoint Tracking Workflow

```text
Start with IP Address
        ↓
Generate Traffic
        ↓
show arp
        ↓
Obtain MAC Address
        ↓
show mac address-table
        ↓
Locate Switchport
        ↓
Follow Trunk if Necessary
        ↓
Identify Endpoint Port
```

---

# Endpoint Documentation Table

| Device           | IP Address    | MAC Address | Switch | Interface | Description |
| ---------------- | ------------- | ----------- | ------ | --------- | ----------- |
| BaristaPOS       | 192.168.42.37 | 5254.0080.35b5         | RW-CORE-SW    | ET0/1       | ## To BaristaPOS ##         |
| InventoryStation | 192.168.42.38 | 5254.008c.e1a5         | RW-ACC-SW    | ET0/1       | ## To InventoryStation ##           |
| ManagerConsole   | 192.168.42.39 | 5254.0035.473a         | RW-ACC-SW    | ET0/2       | ## To ManagerConsole ##           |

---

# Task 4 — Validate Continuity

## Objective

Ensure the network remains operational after discovery and documentation activities.

---

## Validation Requirements

* MAC entries remain present
* Trunk remains operational
* VLAN assignments remain unchanged
* No interfaces entered err-disabled state
* Endpoint connectivity remains intact

---

## Verification Commands

### Verify MAC Address Table

```bash
show mac address-table
```

---

### Verify Trunk Status

```bash
show interfaces trunk
```

---

### Verify Interface Health

```bash
show interfaces status
```

---

### Verify VLAN Assignments

```bash
show vlan brief
```

---

# Anomaly Log

Document any issues discovered during the investigation.

| Issue | Device | Interface | Notes |
| ----- | ------ | --------- | ----- |
| No interfaces description  | RW-CORE-SW - RW-ACC-SW  | ET0/0 - 3 & Vlan42      | Descriptions added   |

---

# Final Engineering Notes

## Core Switch

| Item             | Value |
| ---------------- | ----- |
| Hostname         |  RW-CORE-SW     |
| Management IP    |  192.168.42.1     |
| Uplink Interface |  ET0/0       |
| VLANs            |  1,42     |

---

## Access Switch

| Item             | Value |
| ---------------- | ----- |
| Hostname         |   RW-ACC-SW    |
| Management IP    |  192.168.42.2   |
| Default Gateway  |  192.168.42.1   |
| Uplink Interface | ET0/0      |
| VLANs            |  1,42     |

---

# Completion Checklist

* [X] RW-CORE-SW documented
* [X] RW-ACC-SW documented
* [X] Trunk verified
* [X] VLAN assignments documented
* [X] BaristaPOS located
* [X] InventoryStation located
* [X] ManagerConsole located
* [X] Interface descriptions applied
* [X] Management connectivity documented
* [X] Topology notes completed
* [X] Continuity validated

---

# Key Takeaways

* Switches locate devices using MAC addresses, not IP addresses
* ARP bridges the gap between Layer 3 and Layer 2 information
* MAC address tables reveal where devices physically connect
* CDP helps trace devices across multiple switches
* Accurate interface descriptions improve operational efficiency
* Network discovery is a critical skill before automation deployment
* Proper documentation reduces troubleshooting time and operational risk





