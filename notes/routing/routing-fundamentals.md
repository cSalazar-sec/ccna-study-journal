# Routing Fundamentals

---

# What is Routing?

Routing is the process routers use to determine the path IP packets should take across a network to reach their destination.

Routers make forwarding decisions based on information stored in the:

```text
Routing Table
```

---

# What Does a Router Do?

When a router receives a packet:

1. It examines the destination IP address
2. It checks the routing table
3. It selects the best matching route
4. It forwards the packet toward the destination

---

# The Routing Table

Routers store routes to known destinations in the routing table.

Each route acts like an instruction:

```text
"To reach destination X, send the packet to next-hop Y."
```

---

# What is a Next-Hop?

The:

```text
Next-Hop
```

is the next router in the path toward the destination network.

Instead of knowing the full end-to-end path, routers typically only need to know:
- Where to send the packet next

---

# Main Routing Methods

Routers learn routes in two primary ways:

---

# 1. Dynamic Routing

Routers automatically exchange routing information using routing protocols.

Examples:
- OSPF
- EIGRP
- BGP
- RIP

Advantages:
- Automatic route learning
- Easier scalability
- Automatic adaptation to topology changes

---

# 2. Static Routing

A network engineer manually configures routes on the router.

Advantages:
- Simplicity
- Predictability
- Lower overhead
- Useful for small or stable environments

Disadvantages:
- Manual maintenance
- Poor scalability in large networks

---

# Local and Connected Routes

When you configure an IP address on a router interface and enable it using:

```bash
no shutdown
```

the router automatically adds:

- One Connected Route
- One Local Route

for each interface.

---

# Connected Routes

```text
Code: C
```

A connected route is a route to the network directly connected to the interface.

Example:

If an interface has:

```text
192.168.1.1/24
```

the router automatically creates:

```text
192.168.1.0/24
```

Connected routes tell the router:

> “This network is directly reachable through this interface.”

---

# Local Routes

```text
Code: L
```

A local route is a route to the router’s exact interface IP address.

Example:

If the interface IP is:

```text
192.168.1.1/24
```

the router creates:

```text
192.168.1.1/32
```

Local routes tell the router:

> “Packets sent to this IP belong to me.”

The router receives these packets locally instead of forwarding them.

---

# Viewing the Routing Table

Use:

```bash
show ip route
```

to display the routing table.

---

# Common Route Codes

| Code | Meaning |
|---|---|
| L | Local Route |
| C | Connected Route |
| S | Static Route |
| O | OSPF Route |

---

# Example Routing Table Entries

```text
C 192.168.1.0/24 is directly connected, GigabitEthernet0/0
L 192.168.1.1/32 is directly connected, GigabitEthernet0/0
```

---

# Route Matching

A route matches a packet if the destination IP belongs to the network specified in the route.

Example:

A packet destined for:

```text
192.168.1.60
```

matches:

```text
192.168.1.0/24
```

but does NOT match:

```text
192.168.0.0/24
```

---

# What Happens if No Route Exists?

If a router receives a packet and:
- No matching route exists

the router:

```text
Drops the packet
```

This is different from switches.

Switches flood unknown frames when they do not know the destination MAC address.

Routers do NOT flood unknown IP traffic.

---

# Route Selection

Sometimes multiple routes match the same destination.

In this case, the router selects the:

```text
Most Specific Route
```

also called:

```text
Longest Prefix Match
```

---

# Longest Prefix Match

The route with the:
- Longest subnet mask
- Most matching bits

wins.

---

# Example

Given these routes:

```text
192.168.0.0/16
192.168.1.0/24
```

A packet destined for:

```text
192.168.1.50
```

matches BOTH routes.

However:

```text
192.168.1.0/24
```

wins because:
- /24 is more specific than /16

---

# Routing vs Switching

| Switching | Routing |
|---|---|
| Uses MAC addresses | Uses IP addresses |
| Layer 2 | Layer 3 |
| Exact MAC match | Longest prefix match |
| Floods unknown frames | Drops unknown packets |

---

# Operational Troubleshooting Mindset

When troubleshooting routing problems, always ask:

- Does the router have a route to the destination?
- Is the route correct?
- Is the next-hop reachable?
- Is the interface up?
- Is there a more specific route overriding another route?

Most routing problems become much easier once you understand how routers think.

---

# Key Takeaways

- Routers use routing tables to forward packets
- Routes tell routers where to send traffic
- Connected and local routes are added automatically
- Connected routes represent directly connected networks
- Local routes represent the router’s own IP addresses
- Routers choose the most specific matching route
- If no matching route exists, the router drops the packet
- Routing decisions are based on IP addresses and prefix lengths
