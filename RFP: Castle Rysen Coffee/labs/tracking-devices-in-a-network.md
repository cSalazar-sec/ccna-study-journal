# Tracking Devices in a Network

---

# Lab Overview

This lab focuses on identifying and tracing devices through a switched network using only switch-level access.

The primary objective is to locate devices:
- By IP address
- By MAC address
- Down to the exact switchport

using operational troubleshooting techniques commonly performed by network engineers.

---

# Lab Objectives

- Trace devices through a Layer 2 switched environment
- Translate IP addresses into MAC addresses
- Use ARP tables and MAC address tables operationally
- Identify uplinks between switches
- Navigate multi-switch environments
- Practice real-world troubleshooting workflows

---

# Scenario 1 — Slow Internet Complaint

A user with the IP address:

```text
192.168.1.18
```

reported slow internet access.

The task was to:
- Locate the device
- Identify its MAC address
- Determine the exact switch and switchport where the device connects
<img width="371" height="210" alt="image" src="https://github.com/user-attachments/assets/25e3a2ab-b097-4e54-88f6-7878eced29f0" />


---

# Scenario 2 — Security Investigation

The IP address:

```text
192.168.1.11
```

was identified as the source of suspicious activity and a potential security breach.

The task was to:
- Locate the device physically within the network
- Identify the access switch and port
- Assist security teams with malware investigation and containment
<img width="370" height="221" alt="image" src="https://github.com/user-attachments/assets/20383e11-c67a-4828-bae9-a4197a0365a4" />


---

# Commands Used

The following IOS commands were used throughout the investigation:

---

## View the ARP Table

```bash
show arp
```

Used to:
- Translate IP addresses into MAC addresses

---

## View MAC Address Table

```bash
show mac address-table
```

Used to:
- Determine which switchport learned a MAC address

---

## View CDP Neighbors

```bash
show cdp neighbors
```

Used to:
- Identify neighboring Cisco devices
- Determine uplink paths between switches

---

# Troubleshooting Workflow

The investigation process followed this operational workflow:

---

# Step 1 — Start With the IP Address

Example:

```text
192.168.1.18
```

Switches do not primarily operate using IP addresses.

Switches operate using:

```text
MAC Addresses
```

So the first task becomes:
- Translate the IP into a MAC address

---

# Step 2 — Generate Traffic

If the device appears inactive:
- Generate traffic intentionally

Example:

```bash
ping 192.168.1.18
```

This refreshes:
- ARP entries
- MAC address table entries

A quiet device is significantly harder to locate than an active one.

---

# Step 3 — Use ARP to Learn the MAC Address

Run:

```bash
show arp
```

Example result:

```text
Internet 192.168.1.18  0   0050.7966.6800  ARPA
```

Now the device’s MAC address is known.

---

# Step 4 — Search the MAC Address Table

Run:

```bash
show mac address-table
```

This reveals:
- Which switchport learned the MAC address

Example:

```text
0050.7966.6800  dynamic  Fa0/24
```

---

# Step 5 — Determine if the Port is an Access Port or Uplink

If:
- Only one MAC address appears on the interface

it is likely:
- An endpoint device

If:
- Multiple MAC addresses appear behind the interface

it is likely:
- An uplink to another switch

This means the device exists further downstream.

---

# Step 6 — Use CDP to Identify Neighboring Switches

Run:

```bash
show cdp neighbors
```

This identifies:
- Connected Cisco switches
- Neighboring devices
- Uplink relationships

Move to the next switch and repeat the process.

---

# End-to-End Investigation Flow

```text
Start with IP Address
        ↓
Generate Traffic (Ping)
        ↓
Use show arp
        ↓
Find MAC Address
        ↓
Use show mac address-table
        ↓
Find Switchport
        ↓
Use show cdp neighbors → If uplink
        ↓
Move to next switch
        ↓
Repeat until endpoint is found
```

---

# Important Operational Concepts

---

# ARP Translates IP to MAC

```text
ARP = Address Resolution Protocol
```

ARP allows devices to:
- Discover MAC addresses for local IP addresses

Without ARP:
- Switch-level tracking becomes much harder

---

# Switches Think in MAC Addresses

Routers primarily care about:
- IP addresses

Switches primarily care about:
- MAC addresses

Once the IP is translated into a MAC address:
- The switch can locate the device using its CAM table

---

# MAC Address Tables Age Out

MAC address entries are not permanent.

If a device becomes inactive:
- The switch may remove the entry after several minutes

Similarly:
- ARP cache entries also expire

---

# Why Generating Traffic Helps

If the device disappears from the tables:
- Ping it
- Make it communicate
- Force the switch and router to relearn the information

This is an extremely common real-world troubleshooting technique.

---

# Network Diagram

<img width="1483" height="765" alt="image" src="https://github.com/user-attachments/assets/5a18ce4f-01ce-4feb-a525-0e3049350c08" />


---

# Key Takeaways

- Device tracking often starts with an IP address
- ARP translates IP addresses into MAC addresses
- Switches use MAC address tables to learn device locations
- CDP helps identify neighboring Cisco devices
- MAC tables and ARP caches age out over time
- Generating traffic refreshes learned entries
- Multiple MAC addresses on one port usually indicate an uplink
- Effective troubleshooting is a step-by-step investigative process
