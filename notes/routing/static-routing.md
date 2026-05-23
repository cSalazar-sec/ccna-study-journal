# Static Routing

---

# What is Static Routing?

Static routes allow routers to send packets to remote destinations that are not directly connected to the router itself.

Unlike connected and local routes:

```text
Static routes are NOT added automatically.
```

They must be manually configured by a network engineer.

---

# Why Static Routes Matter

Routers only know:
- Directly connected networks
- Routes learned dynamically
- Routes manually configured

If a router does not have a route to a destination:
- It drops the packet

Static routes allow us to manually teach routers where remote networks exist.

---

# The Default Gateway

End devices usually do not need detailed routing information.

They only need to know:

```text
"To reach anything outside my local network, send traffic to my default gateway."
```

The default gateway is typically:
- A router interface on the local LAN

---

# How Routers Forward Packets

When a router receives a packet:

1. It removes the Layer 2 header and trailer
2. It examines the destination IP address
3. It checks the routing table
4. It selects the most specific matching route
5. It forwards the packet

If no matching route exists:
- The router drops the packet

---

# Static Route Configuration

Basic syntax:

```bash
ip route <destination-network> <netmask> <next-hop>
```

Example:

```bash
ip route 192.168.1.0 255.255.255.0 192.168.15.4
```

Meaning:

```text
"To reach 192.168.1.0/24, send packets to next-hop 192.168.15.4"
```

---

# Static Routes Using Exit Interfaces

Instead of specifying a next-hop IP address, you can specify:

```text
An Exit Interface
```

Example:

```bash
ip route 192.168.1.0 255.255.255.0 g0/1
```

This tells the router:

```text
"Send packets for this destination out interface g0/1"
```

---

# Static Route Using Both

You can also configure:
- The exit interface
- AND the next-hop IP

Example:

```bash
ip route 192.168.1.0 255.255.255.0 g0/1 192.168.15.4
```

This is often considered the most explicit and reliable configuration method.

---

# Interesting Routing Table Behavior

When a static route is configured using only an exit interface:

```bash
ip route 192.168.1.0 255.255.255.0 g0/1
```

the routing table may display the destination network as:

```text
Directly Connected
```

Even though:
- The network is NOT truly directly connected

This is simply how IOS represents that type of static route internally.

---

# Default Route

The:

```text
Default Route
```

is also known as:

```text
0.0.0.0/0
```

This route represents:

```text
ALL possible IPv4 addresses
```

---

# What a Default Route Means

A default route basically says:

> “If there are no more specific matching routes, send the packet here instead.”

This prevents routers from dropping packets when no specific route exists.

---

# Why the Default Route is Special

The default route:

```text
0.0.0.0/0
```

is the:

```text
Least Specific Route Possible
```

because:
- The prefix length is `/0`
- No bits are fixed
- It matches every IPv4 address

---

# Route Specificity

| Route | Prefix Length | Specificity |
|---|---|---|
| 0.0.0.0/0 | /0 | Least specific |
| 192.168.1.0/24 | /24 | More specific |
| 192.168.1.1/32 | /32 | Most specific |

A:

```text
/32 route
```

matches only one IP address.

That is why local routes are extremely specific.

---

# Static Default Route

A router can also use a static route as its default route.

Example:

```bash
ip route 0.0.0.0 0.0.0.0 192.168.15.4
```

Meaning:

```text
"If no better route exists, send packets to 192.168.15.4"
```

This is extremely common for:
- Internet connectivity
- Branch routers
- Small networks

---

# Static Routing Advantages

| Advantage | Explanation |
|---|---|
| Simple | Easy to understand |
| Predictable | Routes never change automatically |
| Low overhead | No routing protocol traffic |
| Secure | No route advertisements |

---

# Static Routing Disadvantages

| Disadvantage | Explanation |
|---|---|
| Manual configuration | Requires administrative effort |
| Poor scalability | Difficult in large networks |
| No automatic adaptation | Failures require manual changes |
| Harder maintenance | Large routing tables become difficult |

---

# Understanding the Default Route

A:

```text
Default Route
```

is a route to:

```text
0.0.0.0/0
```

This is the:

```text
Least Specific Route Possible
```

because it matches:
- Every IPv4 destination address

---

# How the Default Route Works

If a router:
- Does NOT have a more specific matching route

then:
- The router forwards the packet using the default route

You can think of it as:

> “If you don’t know exactly where this destination is, send it here.”

---

# Default Routes and Internet Access

Default routes are extremely common for:

```text
Internet-bound traffic
```

Typically:
- Internal corporate destinations use specific routes
- Everything else gets forwarded toward the internet

Example:

```bash
ip route 0.0.0.0 0.0.0.0 203.0.113.1
```

Meaning:

```text
"If no better route exists, send traffic to 203.0.113.1"
```

This next-hop is often:
- An ISP router
- An upstream firewall
- An edge router

---

# Candidate Default Route

In the routing table, you may see:

```text
* - Candidate Default
```

This means:

```text
"This route is eligible to become the router's default route."
```

Routers can actually have:
- Multiple candidate default routes

The router then selects the best one based on:
- Administrative distance
- Routing protocol metrics
- Route preference

This becomes especially important in:
- Redundant WAN designs
- Dynamic routing environments
- ISP failover scenarios

---

# Load Balancing and Backup Paths

Static routing can also support:
- Load balancing
- Backup routes
- Redundant paths

Example concepts:
- Path 1 = primary route
- Path 2 = backup route

This allows networks to:
- Improve resiliency
- Continue operating during failures

---

# Floating Static Routes

A:

```text
Floating Static Route
```

is a backup static route with a higher administrative distance.

The router only uses it if the primary route fails.

This is a very common redundancy design.

---

# Routing Decision Process

Routers generally follow this logic:

1. Look for the most specific route
2. If multiple equal routes exist, load balancing may occur
3. If no specific route exists, use the default route
4. If no default route exists, drop the packet

---

# Operational Troubleshooting Mindset

When troubleshooting static routing issues, always verify:

- Does the route exist?
- Is the next-hop reachable?
- Is the interface up?
- Is the subnet mask correct?
- Is there a more specific route overriding another route?
- Is the default route configured properly?

---

# Key Takeaways

- Static routes are manually configured routes
- They allow routers to reach remote networks
- Routers forward packets using the most specific matching route
- Default routes act as a “catch-all” route
- Exit interfaces can be used instead of next-hop IPs
- Static routes can support redundancy and backup paths
- If no matching route exists, routers drop packets
- `ip route` is the core static route configuration command
