# Configuring Static NAT

## Scenario

The Coffee House LAN has connectivity to the ISP, but hosts on the private network still cannot communicate with the simulated Internet.

The problem is that the ISP only knows how to reach public IP addresses. It has no route back to the private `192.168.1.0/24` network. As a result, outbound traffic may leave the Coffee House network, but return traffic has nowhere to go.

To solve this problem, I configured **Static NAT (Network Address Translation)**.

Static NAT creates a permanent one-to-one mapping between a private IP address and a public IP address. This allows internal devices to be reachable from external networks while maintaining a predictable address assignment.

---

## Network Diagram

<img width="1872" height="840" alt="image" src="https://github.com/user-attachments/assets/3fc7486d-8efa-4162-b3cf-53cb51429b92" />

---

## Addressing Plan

| Device | Private Address | Public NAT Address |
|----------|----------|----------|
| PC1 | 192.168.1.50 | 216.0.5.20 |
| PC2 | 192.168.1.51 | 216.0.5.21 |
| Cafe-Rtr (LAN) | 192.168.1.1/24 | N/A |
| Cafe-Rtr (WAN) | 216.0.5.2/30 | N/A |
| ISP-Rtr | 216.0.5.1/30 | N/A |

---

## Lab Objectives

- Troubleshooting Internet connectivity issues
- Understanding why NAT is required
- Configuring Static NAT
- Identifying Inside Local and Inside Global addresses
- Configuring NAT Inside and NAT Outside interfaces
- Verifying NAT translations
- Testing inbound and outbound connectivity

---

## Task 0 – Assess Pre-NAT Behavior

Before configuring NAT, I verified the existing network connectivity.

### Verify Interface Status

<img width="713" height="74" alt="image" src="https://github.com/user-attachments/assets/e766a627-2e74-477a-9b51-9c274d09ac62" />

```text
LAN: 192.168.1.0/24
WAN: 216.0.5.0/30
```

---

### Test Internet Reachability

From PC1:

```bash
ping -c 5 1.1.1.1
```

<img width="543" height="90" alt="image" src="https://github.com/user-attachments/assets/0a3ba6c3-1971-4a63-b203-29e7a4cccf00" />

---

### Investigate the ISP Router

The ISP router already had routes for:

```text
216.0.5.20
216.0.5.21
```

<img width="522" height="115" alt="image" src="https://github.com/user-attachments/assets/830c136b-b153-480d-822a-02ae3b7bc4c9" />

However, it did **not** have a route to:

```text
192.168.1.0/24
```

This means:

- The ISP can reach public addresses
- The ISP cannot reach private addresses
- Return traffic will fail

This is why NAT is required.

---

## Understanding the Translation

Static NAT creates a permanent one-to-one mapping.

| NAT Term | Address |
|-----------|-----------|
| Inside Local | 192.168.1.50 |
| Inside Global | 216.0.5.20 |

In simple terms:

```text
Private IP → Public IP
192.168.1.50 → 216.0.5.20
```

External devices only see the public address.

---

## Task 1 – Create the Static NAT Mapping

I configured a permanent translation for PC1.

### Configure Static NAT

<img width="654" height="72" alt="image" src="https://github.com/user-attachments/assets/47adbe48-6723-45fc-b477-aa7e02c0a793" />

This tells the router:

> Whenever traffic comes from 192.168.1.50, translate it to 216.0.5.20.

It also allows inbound traffic sent to 216.0.5.20 to be forwarded to 192.168.1.50.

---

## Task 2 – Configure NAT Inside and Outside Interfaces

NAT requires the router to know which interfaces belong to the private network and which belong to the public network.

### Configure the Inside Interface

```bash
interface ethernet0/0
 ip nat inside
```

---

### Configure the Outside Interface

```bash
interface ethernet0/1
 ip nat outside
```
<img width="301" height="74" alt="image" src="https://github.com/user-attachments/assets/3567f26a-042e-46ef-a907-b78a8138178d" />

---

### Verify Interface Configuration

```bash
show running-config
```

<img width="335" height="168" alt="image" src="https://github.com/user-attachments/assets/dd9517d7-8eb7-4b80-8b30-d8a2b2c85a37" />

---

## Task 3 – Verify NAT Operation

After configuring Static NAT, I tested connectivity again.

### Test from PC1

```bash
ping -c 5 1.1.1.1
```

Expected:

<img width="501" height="200" alt="image" src="https://github.com/user-attachments/assets/cc87be54-1521-466e-bf5d-3f313bcd7b82" />

---

### View NAT Translations

```bash
show ip nat translations
```

<img width="672" height="76" alt="image" src="https://github.com/user-attachments/assets/d248b456-de50-4f7b-89e5-6f1302562b75" />

This confirms the translation is active.

---

### Verify from the ISP Router

From ISP-Rtr:

```bash
ping 216.0.5.20
```

<img width="596" height="91" alt="image" src="https://github.com/user-attachments/assets/dc7314c8-0f18-4d60-8bdd-bfb119fc2d5f" />

The ISP router reaches the public address and Cafe-Rtr forwards the traffic to PC1.

---

## Task 4 – Publish a Second Endpoint

To demonstrate multiple static translations, I repeated the process for PC2.

### Configure Static NAT

<img width="573" height="26" alt="image" src="https://github.com/user-attachments/assets/3013f8c0-c99b-488a-96a8-f3e3ad2a14d8" />

<img width="491" height="198" alt="image" src="https://github.com/user-attachments/assets/c90f46e5-7b2b-49d2-a126-a01cfa52cc77" />

---

### Verify NAT Table

<img width="646" height="94" alt="image" src="https://github.com/user-attachments/assets/cf6c5772-0230-4cf0-8abb-662033bc382b" />

---

## Verification Commands

### Verify NAT Translations

```bash
show ip nat translations
```

---

### Verify NAT Statistics

```bash
show ip nat statistics
```

---

## Verification Checklist

- [X] LAN and WAN interfaces operational
- [X] Static NAT configured for PC1
- [X] Static NAT configured for PC2
- [X] NAT inside interface configured
- [X] NAT outside interface configured
- [X] NAT translations verified
- [X] PC1 can reach 1.1.1.1
- [X] PC2 can reach 1.1.1.1
- [X] ISP can reach 216.0.5.20
- [X] ISP can reach 216.0.5.21
- [X] Configuration saved

---

## What I Practiced

- Static NAT
- One-to-one address translation
- NAT terminology
- NAT verification
- Connectivity troubleshooting
- Internet access troubleshooting
- Inbound and outbound NAT validation

---

## Key Takeaways

- Static NAT creates a permanent one-to-one mapping between a private IP address and a public IP address.
- The private address is called the **Inside Local** address.
- The public address is called the **Inside Global** address.
- NAT requires interfaces to be identified as inside or outside.
- Static NAT allows external devices to initiate communication toward internal hosts.
- ISPs generally do not route private address space across the Internet.
- NAT solves the return-path problem by translating private addresses into publicly routable addresses.
- `show ip nat translations` is one of the most important commands when troubleshooting NAT.
