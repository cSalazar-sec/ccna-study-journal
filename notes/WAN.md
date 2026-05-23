# WAN Technologies

---

# What is a WAN?

```text
WAN = Wide Area Network
```

Is the connectivity that links separate locations across distance.

Once we start connecting:
- Branch offices
- Stores
- Remote campuses
- Data centers
- Cloud environments

we enter WAN territory.

---

# WANs Connect Business Functions

A WAN is not just about connecting buildings.

It is about connecting:
- Employees
- Applications
- Data centers
- Cloud services
- Business operations

that happen to exist in different physical locations.

---

# Common WAN Technologies

WAN connectivity can be built using:
- MPLS
- Metro Ethernet
- Leased Lines
- Internet VPNs
- SD-WAN

The correct solution depends on:
- Cost
- Performance
- Geography
- Scalability
- Business requirements

---

# MPLS

```text
MPLS = Multiprotocol Label Switching
```

MPLS is a provider-managed private WAN service.

Instead of building direct point-to-point links between every office:
- Each site connects into the provider’s MPLS cloud
- The provider handles traffic routing between locations

---

# How MPLS Works

Traffic is separated using:
- Labels
- Virtual circuits

This allows providers to carry traffic for many customers while keeping networks logically isolated.

---

# Important Clarification

MPLS is commonly considered:
- Private
- Secure
- Reliable

However:

```text
Private does NOT automatically mean encrypted.
```

Unlike VPN traffic, MPLS traffic is not necessarily encrypted unless additional security technologies are added.

---

# Why Businesses Used MPLS

Advantages:
- Predictable performance
- Reliable latency
- Quality of Service (QoS)
- Traffic prioritization
- Private WAN connectivity

Historically, MPLS became extremely popular for:
- Branch connectivity
- Voice traffic
- Enterprise WANs

---

# Metro Ethernet

Metro Ethernet is commonly:
- Fiber-based
- High-speed
- Low-latency

It is often used between:
- Corporate offices
- Data centers
- Major sites

---

# Why Metro Ethernet Feels Different

Metro Ethernet is frequently:

```text
Layer 2 Ethernet Connectivity
```

which can make distant sites feel almost like they are connected to the same switching environment.

This is especially useful when:
- High throughput matters
- Low latency matters
- Large data transfers occur frequently

---

# Metro Ethernet Types

| Type | Description |
|---|---|
| E-Line | Point-to-point connectivity |
| E-LAN | Multipoint Ethernet network |
| E-Tree | Hub-and-spoke topology |

---

# MPLS vs Metro Ethernet

The decision should begin with the:

```text
Business requirement
```

not simply the technology name.

---

## Metro Ethernet is Often Better When:

- High-speed site-to-site connectivity is needed
- Connecting data centers
- Low latency matters
- Layer 2 extension is useful

---

## MPLS is Often Better When:

- Many branch offices need private connectivity
- QoS is important
- Centralized carrier management is preferred
- Enterprise WAN routing is needed

---

# Internet WAN and VPNs

Sometimes the WAN is simply:

```text
The Public Internet
```

Instead of paying for private carrier WAN services:
- Businesses purchase internet connections
- Then build encrypted site-to-site VPN tunnels between locations

This dramatically reduces cost.

---

# Site-to-Site VPNs

```text
Site-to-Site VPN
```

Creates encrypted tunnels across the internet between locations.

Advantages:
- Lower cost
- Flexible deployment
- Easy branch connectivity

---

# The Problem With Internet WANs

Internet traffic is:
- Shared
- Unpredictable
- Congested at times

Packets do not receive guaranteed treatment.

This can negatively affect:
- Voice calls
- Video conferencing
- Real-time applications
- Sensitive business traffic

---

# Quality of Service (QoS)

```text
QoS = Quality of Service
```

QoS prioritizes important traffic over less important traffic.

Examples:
- Voice traffic receives priority
- Payroll traffic is protected
- Bulk downloads wait their turn

QoS became one of the major reasons MPLS remained popular for many years.

---

# SD-WAN

```text
SD-WAN = Software-Defined Wide Area Network
```

SD-WAN allows businesses to use internet connections more intelligently.

Instead of relying entirely on expensive private WAN circuits:
- Multiple WAN links can be combined
- Traffic paths can be optimized dynamically
- Applications can be prioritized intelligently

---

# Why SD-WAN Became Important

Modern applications increasingly live in:
- AWS
- Azure
- SaaS platforms
- Cloud environments

Traditional WAN designs assumed:
- Users connected back to a central data center

Modern traffic patterns are different.

SD-WAN improves:
- Cloud connectivity
- Path selection
- Performance
- Resiliency
- WAN flexibility

---

# WAN Evolution

| Older WAN Approach | Modern WAN Approach |
|---|---|
| MPLS-centric | Internet + SD-WAN |
| Centralized data centers | Cloud-first connectivity |
| Expensive private circuits | Flexible hybrid WAN |
| Static routing decisions | Dynamic path optimization |

---

# Key Takeaways

- WANs connect geographically separated locations
- MPLS is a provider-managed private WAN service
- Metro Ethernet often provides high-speed Layer 2 connectivity
- Site-to-site VPNs use the public internet securely
- QoS prioritizes important traffic
- SD-WAN improves WAN intelligence and cloud connectivity
- Modern WANs focus heavily on flexibility and cloud access
