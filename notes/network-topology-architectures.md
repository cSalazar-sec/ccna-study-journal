# Network Topology Architectures

---

# Designing for Failure

Good network design is not about assuming everything works perfectly.

It is about asking:

- What happens if this link fails?
- What happens if this switch dies?
- What happens if this router goes offline?

If one failed cable or switch takes down half the office, you have discovered a weak point in the design.

---

# Redundancy

Redundancy is not simply “adding more cables.”

Real redundancy means:
- Removing single points of failure
- Avoiding dependency on one critical device
- Creating alternate traffic paths
- Keeping the network operational during failures

The goal is resilience.

---

# Avoid Lazy Daisy Chains

Poorly designed networks often look like this:

```text id="d6kqzv"
Switch1 → Switch2 → Switch3 → Switch4
```

This creates multiple problems:
- A single switch failure can disconnect everything downstream
- Increased latency
- Difficult troubleshooting
- Limited scalability

Instead, enterprise networks typically use hierarchical designs.

---

# Two-Tier Architecture

The two-tier model is common in:
- Small businesses
- Medium-sized offices
- Campus environments with simpler requirements

It is also known as:

```text id="8b7dmc"
Collapsed Core Design
```

because the distribution layer performs both:
- Distribution functions
- Core/backbone functions

---

# Layers in a Two-Tier Design

## 1. Access Layer

This is where end devices connect to the network.

Examples:
- PCs
- Printers
- IP Phones
- Wireless Access Points
- Servers at the edge

The access layer’s primary role is:
- Device connectivity
- VLAN membership
- Port security
- Local access

---

## 2. Distribution Layer

The distribution layer acts as:
- The aggregation point for access switches
- The routing boundary between VLANs
- The policy enforcement layer

This layer is commonly built using:

```text id="pyo62z"
Layer 3 (Multilayer) Switches
```

Responsibilities often include:
- Inter-VLAN routing
- Redundancy
- Route summarization
- ACLs
- Traffic policies

---

# Why Two-Tier Designs Work

Advantages:
- Simpler architecture
- Lower cost
- Easier management
- Good for smaller environments

Disadvantages:
- Limited scalability
- Distribution layer becomes very important
- Less ideal for very large campuses

---

# Example Two-Tier Topology

```text id="7jyc2j"
          Distribution Layer
          +---------------+
          | Layer 3 Switch|
          +---------------+
             /    |    \
            /     |     \
           /      |      \
     Access   Access   Access
     Switch   Switch   Switch
```

> Insert topology diagram image here

---

# Three-Tier Architecture

As networks grow larger, the two-tier model may become difficult to scale.

This is where the three-tier architecture becomes important.

The three-tier model separates responsibilities into:
1. Access Layer
2. Distribution Layer
3. Core Layer

---

# 1. Access Layer

This is the end-user layer.

Devices connected here include:
- Workstations
- Printers
- IP Phones
- Wireless Access Points

Responsibilities:
- Device access
- VLAN assignment
- Port-level security
- Edge connectivity

---

# 2. Distribution Layer

The distribution layer acts as the consolidation point.

Access switches connect upward to the distribution layer instead of directly to each other.

This layer commonly handles:
- Inter-VLAN routing
- Redundancy
- Policy enforcement
- Access control
- Route filtering

---

## Redundancy at the Distribution Layer

A common best practice is:

```text id="ydj1f2"
Each access switch connects to BOTH distribution switches.
```

This prevents a single distribution switch failure from taking down users.

If one path fails:
- Traffic reroutes through the secondary path
- The network continues operating

---

# 3. Core Layer

The core layer acts as the high-speed backbone of the network.

Its job is simple:
- Move traffic quickly
- Connect multiple distribution blocks
- Provide scalable interconnection between buildings or campuses

---

# Why the Core Layer Matters

Without a core layer:

- Every building may need direct connections to every other building
- Cabling becomes messy
- Scalability becomes difficult
- Troubleshooting becomes painful

The core solves this by centralizing connectivity.

Instead of:

```text id="dljlhh"
Building A ↔ Building B ↔ Building C
```

everything connects through the core.

Clean. Scalable. Manageable.

---

# Example Three-Tier Topology

```text id="kgbrgj"
               Core Layer
          +------------------+
          |   Core Switches  |
          +------------------+
               /        \
              /          \
     Distribution      Distribution
        Switches          Switches
         /   \             /   \
        /     \           /     \
    Access  Access    Access  Access
    Switch  Switch    Switch  Switch
```

> Insert topology diagram image here

---

# Business vs Technical Design

Network design is not only technical.

It is also business-driven.

Every design decision balances:
- Cost
- Scalability
- Risk
- Downtime tolerance
- Operational complexity

The real question becomes:

> “What failures can the business afford?”

---

# Two-Tier vs Three-Tier Design

A simple rule of thumb:

---

## One or Two Buildings

Use a:

```text
Two-Tier Architecture (Collapsed Core)
```

This design includes:
- Access Layer
- Distribution Layer

In this model:
- The distribution layer also acts as the core
- Simpler management
- Lower cost
- Perfectly acceptable for smaller environments

---

## Three or More Buildings

Add a dedicated:

```text
Core Layer
```

At this point, scalability becomes important.

Without a core layer, every building may require direct connections to every other building, creating a fully meshed design that quickly becomes:
- Expensive
- Complex
- Difficult to troubleshoot
- Hard to scale

The core layer centralizes connectivity and keeps the architecture clean.

---

# Simple Design Philosophy

- Every device connects to the access layer
- Every building has a distribution layer
- Multiple buildings connect through the core layer

---

# Quick Comparison

| Two-Tier Design | Three-Tier Design |
|---|---|
| Smaller environments | Large campus environments |
| Lower cost | Higher scalability |
| Simpler management | Better fault isolation |
| Distribution acts as core | Dedicated core backbone |
| Good for 1–2 buildings | Better for 3+ buildings |

---

# Key Takeaways

- Design networks expecting failure
- Redundancy removes single points of failure
- Two-tier designs combine distribution and core functions
- Three-tier designs improve scalability
- The access layer connects end devices
- The distribution layer enforces policies and routing
- The core layer provides fast backbone connectivity
- Good design reduces downtime and simplifies troubleshooting
