# Coffee House Internet Gateway & Default Route Configuration

## Scenario

The Coffee House network needs Internet connectivity.

To accomplish this, Cafe-RT1 must be connected to the ISP and configured with a default route so traffic destined for unknown networks can be forwarded toward the Internet.

Without a default route, Cafe-RT1 only knows about directly connected and specifically configured routes. Any traffic destined for external networks will be dropped.

---

## Network Diagram

<img width="1615" height="974" alt="image" src="https://github.com/user-attachments/assets/d2f9f72b-4d47-4e45-b90e-8d6cf5d3b77e" />

---

## Addressing Plan

| Device | Interface | IP Address |
|----------|------------|------------|
| Cafe-RT1 | Ethernet0/0 | 192.168.1.1/24 |
| Cafe-RT1 | Ethernet0/1 | 192.168.2.1/30 |
| Cafe-RT1 | Ethernet0/2 | 216.0.5.2/30 |
| ISP Router | Ethernet0/0 | 216.0.5.1/30 |

---

## Lab Objectives

- Verify WAN interface availability
- Configure a public-facing ISP connection
- Configure a default route
- Verify the Gateway of Last Resort
- Test Internet reachability
- Understand how routers handle unknown destinations
- Save and validate the final configuration

---

## Task 0 – Prepare the ISP Link

Before configuring a default route, I first verified that the WAN connection to the ISP was available.

The public uplink was connected to Ethernet0/2 on Cafe-RT1.
<img width="685" height="112" alt="image" src="https://github.com/user-attachments/assets/8470b383-cb92-4cba-a312-0eb452c1c75c" />

### Configure the ISP Address

```bash
interface ethernet0/2
 ip address 216.0.5.2 255.255.255.252
 no shutdown
```

<img width="593" height="165" alt="image" src="https://github.com/user-attachments/assets/c45a8d17-5033-4eee-808c-e0e0c1dec2b6" />

---

### Verify Interface Status

```bash
show ip interface brief
```
<img width="724" height="109" alt="image" src="https://github.com/user-attachments/assets/636c8147-3558-47bb-8125-21838fdf8e67" />

---

## Task 1 – Configure the Default Route

Now that the ISP link is operational, the next step is configuring a default route.

A default route tells the router:

> If you don't know where to send a packet, send it here.

Without a default route, packets destined for unknown networks are dropped.

---

### Configure the Default Route

```bash
ip route 0.0.0.0 0.0.0.0 216.0.5.1
```

This route tells Cafe-RT1:

> Send all unknown traffic to the ISP router at 216.0.5.1.

---

### Verify the Routing Table

<img width="701" height="346" alt="image" src="https://github.com/user-attachments/assets/4402b818-7d69-4cac-a6df-ba2e55431a96" />

---

## Task 2 – Validate Internet Reachability

Before installing the default route, the Coffee House LAN should not be able to reach external destinations.

### Test Before Configuring the Default Route

<img width="509" height="92" alt="image" src="https://github.com/user-attachments/assets/e47808bb-50ef-4b0c-932e-8b0e4075f800" />

This occurs because Cafe-RT1 has no route to external networks.

---

### Test After Configuring the Default Route

<img width="497" height="202" alt="image" src="https://github.com/user-attachments/assets/413cda00-fbe9-4622-82ef-449da8debde6" />

Successful replies confirm traffic is now being forwarded through the ISP connection.

---

## Save Configuration

After validating connectivity, save the configuration.

```bash
copy running-config startup-config
```

or

```bash
write memory
```

---

## Verification Checklist

- [X] Ethernet0/2 configured with 216.0.5.2/30
- [X] WAN interface operational (up/up)
- [X] ISP next-hop reachable
- [X] Default route configured
- [X] Gateway of Last Resort present
- [X] Routing table verified
- [X] Internet connectivity tested
- [X] Configuration saved

---

## What I Practiced

- WAN interface configuration
- Public IP addressing
- Default route configuration
- Gateway of Last Resort verification
- Routing table analysis
- Connectivity testing
- Internet reachability validation

---

## Key Takeaways

- A default route is a route to `0.0.0.0/0`, which matches all possible destination IP addresses.
- The default route is used only when no more specific route exists.
- Routers without a matching route will drop packets.
- The Gateway of Last Resort is the router's fallback path for unknown destinations.
- Most end hosts rely on a default gateway for communication outside their local network.
- Internet connectivity often depends on a properly configured default route.
- Verifying the routing table is one of the first steps when troubleshooting connectivity issues.
