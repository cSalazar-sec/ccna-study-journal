# Network Fundamentals

---

# What Even is a Network?

At its core, a network is simply devices communicating with each other.

Networks allow devices to:
- Share data
- Access resources
- Communicate locally or remotely
- Reach destinations from point A to point B

---

# Core Network Devices

| Device | Purpose |
|---|---|
| Switch | Connects devices inside the same local network |
| Router | Connects different networks together |
| Firewall | Filters traffic and protects networks |
| Wireless Access Point | Allows wireless devices to connect to the network |

---

# What Is a Switch?

A switch connects devices within the same local network (LAN) so they can communicate with each other.

Switches primarily operate at **Layer 2 (Data Link Layer)** of the OSI model and make forwarding decisions using **MAC addresses**.

---

## Key Concepts

- MAC addresses
- VLANs
- Broadcast domains
- CAM table
- Ethernet frames

---

## How a Switch Learns Devices

A switch learns device locations using the **source MAC address** of incoming frames.

It stores this information in the:

```text id="9h55l5"
CAM Table (Content Addressable Memory Table)
```

The switch then forwards traffic based on the **destination MAC address**.

---

## Troubleshooting Mindset

If two devices on the same LAN cannot communicate, one of the first places to investigate is the MAC address table.

Possible issues may include:
- Bad cable
- Disabled switchport
- NIC problems
- Incorrect VLAN assignment
- Physical layer failures
- Data Link layer issues

If the switch never learns the device’s MAC address, that immediately points toward a Layer 1 or Layer 2 problem.

---

# What Is a Router?

A router connects different networks together.

While a switch focuses on local communication using MAC addresses, a router focuses on moving traffic between networks using IP addresses.

---

## Switch vs Router

| Device | Uses | Primary Purpose |
|---|---|---|
| Switch | MAC addresses | Local communication |
| Router | IP addresses | Inter-network communication |

---

## Understanding the Default Gateway

The **default gateway** is typically the router interface connected to your local network.

Without a default gateway:
- Your device can communicate locally
- But it cannot reach remote networks or the internet

Think of the default gateway as the network’s “exit door.”

---

# Local vs Remote Communication

When a device wants to send traffic, it first asks:

> “Is this destination local or remote?”

---

## If the Destination is Local

The device:
1. Uses ARP (Address Resolution Protocol)
2. Learns the destination device’s MAC address
3. Sends the frame to the switch

---

## If the Destination is Remote

The device:
1. Uses ARP to learn the router’s MAC address
2. Sends the frame to the default gateway
3. The router forwards traffic toward the remote destination

Same local process — different destination.

---

# Operational Troubleshooting

If a device can:
- Reach local devices
- But cannot access the internet

One of the first things to verify is the default gateway.

Common gateway-related problems:
- Incorrect gateway address
- Missing default gateway
- Router interface failure
- VLAN mismatch
- Gateway unreachable

---

# Key Takeaways

- Switches handle local traffic using MAC addresses
- Routers connect different networks using IP addresses
- Switches learn MAC addresses dynamically
- Routers act as the path between networks
- The default gateway is essential for off-network communication
- ARP helps devices discover MAC addresses on the local network
- Troubleshooting often starts by identifying whether the issue is Layer 1, Layer 2, or Layer 3

---

# Useful Commands

## View MAC Address Table

```bash
show mac address-table
```

## View Interface Status

```bash
show ip interface brief
```

## Verify ARP Table

```bash
show arp
```
