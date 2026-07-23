# Building and Hardening a Multi-Area OSPF Network

## Overview

The Coffee House and Fallout Shelter networks are now connected through a routed WAN, but the existing OSPF deployment requires a complete rebuild and hardening before it is ready for production.

In this lab, I deployed a clean OSPF configuration from scratch, establish stable neighbor relationships, eliminate common causes of adjacency instability, and redesign the topology into a **multi-area OSPF architecture**.

Finally, **Shelter-R1** will become an **Area Border Router (ABR)**, summarize the bunker networks toward the café, and advertise the Internet default route so the Coffee House automatically learns its gateway of last resort.

This lab combines the most important enterprise OSPF concepts into a single deployment exercise.

---

## Lab Objectives

- Remove legacy routing configurations.
- Deploy a clean single-area OSPF configuration.
- Configure passive interfaces correctly.
- Verify and troubleshoot OSPF neighbor adjacencies.
- Understand OSPF timers and network types.
- Convert a router into an Area Border Router (ABR).
- Configure inter-area route summarization.
- Advertise a default route into OSPF.
- Verify dynamic routing and Internet reachability.

---

## Network Diagram

<img width="1640" height="959" alt="image" src="https://github.com/user-attachments/assets/3ddea747-a511-40f5-99cc-166bba742231" />

---

# Task 1 — Prepare the Routers

## Objective

Remove any legacy routing configuration and verify the network is ready for a fresh OSPF deployment.

---

### Remove Existing Dynamic Routing

On both routers, verify whether any previous routing processes exist.

Removed existing:

- OSPF processes
- EIGRP processes

<img width="300" height="34" alt="image" src="https://github.com/user-attachments/assets/a652400b-20d0-4675-91ae-1120bebdf4a3" />

<img width="329" height="34" alt="image" src="https://github.com/user-attachments/assets/31b6aaf7-290b-400a-bad3-224ceafcb61f" />

At this stage, only directly connected and static routes remained.

---

### Verify Interface Addressing

### Cafe-R1

| Interface | Address |
|-----------|----------|
| Ethernet0/0.10 | 10.0.18.1/27 |
| Ethernet0/1 | 172.16.0.1/30 |

<img width="665" height="37" alt="image" src="https://github.com/user-attachments/assets/b7dbf9b4-bf65-45fb-aaa1-e6364be56816" />

---

### Shelter-R1

| Interface | Address |
|-----------|----------|
| Ethernet0/1 | 172.16.0.2/30 |
| Ethernet0/2.x | 10.0.16.0/23 |

<img width="663" height="74" alt="image" src="https://github.com/user-attachments/assets/9d229dd8-8a7c-42cd-a703-9e11c025241a" />

Every deployed Ethernet interface is up and running.

---

# Task 2 — Deploy the Baseline OSPF Configuration

## Objective

Configure a clean OSPF Area 0 deployment between the Coffee House and the Fallout Shelter.

---

## Configure Cafe-R1

Start OSPF Process 1.

Advertise:

- Admin VLAN
- WAN Transit

<img width="307" height="70" alt="image" src="https://github.com/user-attachments/assets/de0a5412-1ab3-4c2d-81ac-c90deebbd1f6" />

Passive interface so it advertises without forming local adjacencies.

```cisco
passive-interface Ethernet0/0.10
```

---

## Configure Shelter-R1

Enable OSPF.

<img width="317" height="126" alt="image" src="https://github.com/user-attachments/assets/34e7cad3-a579-49f8-8c0b-4d050400c0ba" />

Configure user-facing interfaces as passive.

```cisco
passive-interface Ethernet0/2.10
passive-interface Ethernet0/2.20
```
---

## Verify Neighbor Formation

There was a timer mismatch configuration in Eth0/1 in Cafe-R1, so no adjancy was formed.

Cafe-R1

<img width="654" height="198" alt="image" src="https://github.com/user-attachments/assets/90211733-12a7-4930-aaee-75b44dd62c5b" />


Shelter-R1

<img width="624" height="237" alt="image" src="https://github.com/user-attachments/assets/44cda14d-6588-489a-a145-05359ae45d8b" />

As soon as the changes were effective an adjacency was formed.
<img width="827" height="243" alt="image" src="https://github.com/user-attachments/assets/6dfbe270-29be-44b7-8715-e19394165431" />


---

# Task 3 — Harden the OSPF Adjacency

## Objective

Ensure the WAN link uses consistent OSPF parameters on both routers.

---


## Verify the Network Type

There was a point-to-point configuration, I removed it.

<img width="341" height="76" alt="image" src="https://github.com/user-attachments/assets/1d8da986-36db-42b2-9ee5-274f682333d8" />

<img width="1189" height="90" alt="image" src="https://github.com/user-attachments/assets/97e1fedc-4b93-422e-bd58-9f0117203606" />

---

The adjacency should progress through:

```
Down

↓

Init

↓

2-Way

↓

ExStart

↓

Exchange

↓

Loading

↓

FULL
```

Confirmed remote routes remain installed throughout the process.

Cafe-R1

<img width="666" height="450" alt="image" src="https://github.com/user-attachments/assets/bceb90a4-6376-402d-8687-6aac85819981" />

Shelter-R1

<img width="696" height="525" alt="image" src="https://github.com/user-attachments/assets/87fe3c9f-d03f-469e-ab84-3c7816cac1b0" />

---

# Task 4 — Convert to Multi-Area OSPF

## Objective

Promote Shelter-R1 into an Area Border Router.

---

### Final Area Design

| Network | Area |
|----------|------|
| Shelter VLANs | Area 0 |
| WAN Transit | Area 1 |
| Cafe Admin VLAN | Area 1 |

---

## Configure Shelter-R1

Move the WAN interface into Area 1.

<img width="415" height="140" alt="image" src="https://github.com/user-attachments/assets/7a4b08b8-b950-4269-a794-9f78a0989fa2" />


---

## Configure Cafe-R1

Move both networks into Area 1.

<img width="408" height="87" alt="image" src="https://github.com/user-attachments/assets/21b9be0e-09b8-4e87-80f7-b7fa01857acb" />

Retained the passive interface configuration.

---

## Verify the New Topology


### Cafe-R1

<img width="536" height="522" alt="image" src="https://github.com/user-attachments/assets/7492473f-d5ae-4096-83eb-cbbd08a98987" />

### Shelter-R1

<img width="529" height="578" alt="image" src="https://github.com/user-attachments/assets/ee43f4a0-9618-4900-bd27-62ca1c8274b5" />


The OSPF neighbor relationship returned to the **FULL** state.

---

# Task 5 — Summarize the Shelter and Advertise the Default Route

## Objective

Reduce inter-area routing updates and provide Internet reachability to the Coffee House.

---

## Configure Route Summarization

On Shelter-R1:

<img width="549" height="37" alt="image" src="https://github.com/user-attachments/assets/622c411a-6b70-43ab-817a-ab5c576e947a" />

This summarizes the bunker networks before advertising them into Area 1.

---

## Verify the Summary

On Cafe-R1:

<img width="684" height="433" alt="image" src="https://github.com/user-attachments/assets/1b3ae324-6436-4ae6-bc61-74cc8606f11d" />

Instead of multiple inter-area routes, the routing table displayed a single summarized route.

---

## Advertise the Default Route

Shelter-R1 has a static default route.

<img width="666" height="311" alt="image" src="https://github.com/user-attachments/assets/098927df-05ad-4237-b2e8-dbd49be6049e" />

Advertise the route into OSPF.

<img width="485" height="39" alt="image" src="https://github.com/user-attachments/assets/518f78aa-9b8c-4e23-8d42-ffae1d69f247" />

---

## Verify Default Route Propagation

On Cafe-R1:

<img width="735" height="309" alt="image" src="https://github.com/user-attachments/assets/674e4dd4-510b-4268-ab76-10d89a07c31e" />

The next hop should be **Shelter-R1**.

---

## Verify Internet Reachability

Test connectivity toward the ISP.

<img width="595" height="95" alt="image" src="https://github.com/user-attachments/assets/736f99d1-1928-42b0-85b4-5c778ba62360" />

Traffic should follow:

```
Cafe-R1

↓

Shelter-R1

↓

ISP-Edge
```

---

# Understanding the Design

## Phase 1 — Single Area OSPF

```
           Area 0

Cafe-R1 -------- Shelter-R1
```

Both routers share one link-state database.

---

## Phase 2 — Multi-Area OSPF

```
            Area 1

Cafe-R1

      |

      |

----------------------

     Shelter-R1
        (ABR)

----------------------

            Area 0

 Shelter VLANs
```

The ABR maintains a separate link-state database for each area and exchanges summarized routes between them.

---

## Route Summarization

Without summarization:

```
10.0.16.0/24
10.0.17.0/24
```

With summarization:

```
10.0.16.0/22
```

Summarization reduces:

- Link-State Advertisements (LSAs)
- Routing table size
- CPU utilization
- SPF calculations

---

# Verification Checklist

- [X] Legacy routing protocols removed.
- [X] Interfaces verified and operational.
- [X] OSPF Area 0 configured successfully.
- [X] Neighbor adjacency reaches FULL.
- [X] Hello and Dead timers match.
- [X] Network type is Broadcast.
- [X] Shelter-R1 operates as an Area Border Router.
- [X] Cafe-R1 participates only in Area 1.
- [X] Shelter VLANs remain in Area 0.
- [X] Shelter routes are summarized toward Area 1.
- [X] Default route is advertised through OSPF.
- [X] Cafe-R1 installs the dynamic gateway of last resort.
- [X] Internet reachability is successfully verified.

---

# Key Takeaways

- OSPF neighbors must agree on key parameters such as area ID, hello/dead timers, network type, and authentication before an adjacency can form.
- Passive interfaces advertise connected networks while suppressing OSPF Hello packets toward end devices.
- An **Area Border Router (ABR)** connects multiple OSPF areas and maintains a separate link-state database for each one.
- Route summarization using the **`area range`** command reduces the number of inter-area LSAs and improves scalability.
- The **`default-information originate`** command allows an ABR to distribute an existing static default route to downstream routers, enabling automatic Internet reachability throughout the OSPF domain.
- A properly designed multi-area OSPF deployment reduces routing overhead, accelerates convergence, and scales significantly better than a flat single-area topology.
