# Configuring NAT Overload (PAT)

## Scenario

The Coffee House network previously used Dynamic NAT with a pool of public IP addresses. While that solution conserved public addresses compared to Static NAT, each internal host still required its own public IP address while communicating externally.

To improve address utilization even further, I replaced Dynamic NAT with **NAT Overload**, also known as **Port Address Translation (PAT)**.

PAT allows multiple internal devices to share a single public IP address by tracking sessions using unique source port numbers.

This is the most common form of NAT used in modern networks and home Internet connections.

---

## Network Diagram

<img width="1872" height="840" alt="image" src="https://github.com/user-attachments/assets/add1af39-3c2d-4a6b-aa69-bb42907877e3" />

---

## Addressing Plan

### Internal Network

| Device | IP Address |
|----------|----------|
| PC1 | 192.168.1.50 |
| PC2 | 192.168.1.51 |
| Cafe-Rtr LAN Interface | 192.168.1.1/24 |

### WAN Connection

| Device | Interface | IP Address |
|----------|----------|----------|
| Cafe-Rtr | Ethernet0/1 | 216.0.5.2 |
| ISP-Rtr | Ethernet0/0 | 216.0.5.1 |

---

## Lab Objectives

- Removing Dynamic NAT
- Configuring NAT Overload (PAT)
- Reusing ACLs for NAT
- Using interface-based NAT
- Verifying overloaded translations
- Understanding port-based address sharing
- Monitoring NAT statistics

---

## Task 0 – Remove Dynamic NAT

Before enabling PAT, I removed the Dynamic NAT configuration from the previous lab.

### Review Existing NAT Configuration

```bash
show running-config | include nat
```

Existing Dynamic NAT rule and Remove Dynamic NAT:

<img width="566" height="106" alt="image" src="https://github.com/user-attachments/assets/6bad16e6-9388-4ecc-8e4c-6aaad98a0388" />

---

### Verify NAT Configuration

```bash
show running-config | include nat
```

<img width="561" height="85" alt="image" src="https://github.com/user-attachments/assets/03f5143d-d70e-4e47-9333-86e468a100f1" />

The dynamic pool association should no longer appear.

---

### Clear Existing Translations

```bash
clear ip nat translation *
```

---

## Task 1 – Temporarily Disable Interfaces

To prevent new translations from forming while reconfiguring NAT, I temporarily disabled both router interfaces.

### Disable LAN and WAN Interfaces

<img width="826" height="121" alt="image" src="https://github.com/user-attachments/assets/e39285f0-972b-48e6-a44c-a98cf942ab74" />


---

## Task 2 – Configure NAT Overload

Rather than assigning hosts addresses from a public pool, PAT uses the IP address configured on the WAN interface.

### Configure PAT

<img width="570" height="20" alt="image" src="https://github.com/user-attachments/assets/49c33743-295e-4f1d-b35c-48a20fcfa34f" />

This tells the router:

> Any host matched by ACL 1 should use the Ethernet0/1 address when accessing external networks.

---

### Verify NAT Direction

LAN & WAN Interfaces:

<img width="352" height="193" alt="image" src="https://github.com/user-attachments/assets/1ef3debd-d721-4a6d-b110-2219852c2f2a" />

---

### Verify Configuration

<img width="561" height="90" alt="image" src="https://github.com/user-attachments/assets/6251e8f1-1fc0-499a-967a-34e1392d558a" />

---

## Understanding PAT

With PAT, multiple devices share a single public IP address.

Example:

| Internal Host | Private Address |
|---------------|----------------|
| PC1 | 192.168.1.50 |
| PC2 | 192.168.1.51 |

Both devices appear externally as:

```text
216.0.5.2
```

The router distinguishes sessions using unique source port numbers.

Example:

```text
192.168.1.50:1025 → 216.0.5.2:30001

192.168.1.51:1025 → 216.0.5.2:30002
```

Same public IP.

Different port numbers.

---

## Task 3 – Restore Connectivity

After configuring PAT, I re-enabled both interfaces.

### Enable LAN & WAN Interfaces

<img width="822" height="154" alt="image" src="https://github.com/user-attachments/assets/11b0d3ac-37da-483e-b8ef-103f08a7ad99" />

---

## Task 4 – Generate Traffic

To trigger NAT translations, I generated traffic from both hosts.

### PC1

Test:

<img width="471" height="188" alt="image" src="https://github.com/user-attachments/assets/4d8f0d26-b235-47a8-93d3-c2c9bff99f8d" />

---

### PC2

Test:

<img width="467" height="187" alt="image" src="https://github.com/user-attachments/assets/05538fab-c9a5-4c13-8a79-b9f126b5f35b" />


Both hosts successfully generated outbound traffic.

---

## Verify NAT Overload Translations

While traffic was active:

```bash
show ip nat translations
```

<img width="602" height="87" alt="image" src="https://github.com/user-attachments/assets/ef90fdb7-ac60-458f-8f85-f45713c0e3ec" />


Notice:

- Both devices share the same public address
- Unique identifiers distinguish the sessions
- No public address pool is required

---

## Verify NAT Statistics

```bash
show ip nat statistics
```

<img width="512" height="274" alt="image" src="https://github.com/user-attachments/assets/8b127456-de2d-4172-a627-041b15ef496c" />

---

## Dynamic NAT vs PAT

| Feature | Dynamic NAT | PAT (NAT Overload) |
|----------|----------|----------|
| Public IPs Required | Multiple | One |
| Uses NAT Pool | Yes | No |
| Uses Interface Address | No | Yes |
| Address Conservation | Good | Excellent |
| Most Common Today | No | Yes |

---

## Verification Checklist

- [X] Dynamic NAT removed
- [X] Translation table cleared
- [X] ACL 1 reused
- [X] PAT configured
- [X] LAN interface configured as NAT Inside
- [X] WAN interface configured as NAT Outside
- [X] Interfaces restored to service
- [X] PC1 successfully translated
- [X] PC2 successfully translated
- [X] NAT translations verified
- [X] NAT statistics verified
- [X] Configuration saved

---

## What I Practiced

- NAT Overload (PAT)
- Interface-based NAT
- ACL-based translation
- NAT verification
- Translation table analysis
- Connectivity testing
- NAT troubleshooting

---

## Key Takeaways

- NAT Overload (PAT) allows many internal devices to share a single public IP address.
- PAT differentiates sessions using source port numbers.
- PAT is far more efficient than Static NAT or Dynamic NAT.
- Most enterprise and home Internet connections use PAT.
- The `overload` keyword enables port-based translation.
- PAT typically uses the IP address assigned to the WAN interface.
- `show ip nat translations` and `show ip nat statistics` are the primary verification commands when troubleshooting PAT.
- PAT dramatically conserves public IPv4 addresses and is one of the reasons IPv4 remains usable today.
