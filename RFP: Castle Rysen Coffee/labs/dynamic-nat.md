# Configuring Dynamic NAT

## Scenario

The Coffee House network previously used Static NAT to publish individual hosts to the Internet. While that worked, it required a dedicated public IP address for every internal device.

To make better use of the available public address space, I replaced the static mappings with **Dynamic NAT**.

With Dynamic NAT, internal devices temporarily borrow an address from a configured public IP pool whenever they need to communicate with external networks. Once the session ends, the address can be reused by another device.

---

## Network Diagram

<img width="1872" height="840" alt="image" src="https://github.com/user-attachments/assets/46830b55-3c08-452b-8300-285f18d03ecc" />

---

## Addressing Plan

### Internal Network

| Device | IP Address |
|----------|----------|
| PC1 | 192.168.1.50 |
| PC2 | 192.168.1.51 |
| Cafe-Rtr LAN Interface | 192.168.1.1/24 |

### Public NAT Pool

| Pool Name | Range |
|------------|------------|
| Cafe-Public | 216.0.5.50 - 216.0.5.100 |

---

## Lab Objectives

- Removing Static NAT entries
- Creating Standard ACLs
- Configuring Dynamic NAT
- Building NAT pools
- Associating ACLs with NAT pools
- Verifying NAT translations
- Monitoring address pool utilization

---

## Task 0 – Remove Existing Static NAT Entries

Before configuring Dynamic NAT, I removed the Static NAT entries from the previous lab.

### Review Existing NAT Configuration

```bash
show running-config | include ip nat
```
<img width="484" height="92" alt="image" src="https://github.com/user-attachments/assets/52e40a94-08de-4be7-b23e-d0f3cd53e951" />


### Remove Static Translations

<img width="617" height="91" alt="image" src="https://github.com/user-attachments/assets/200cd7d7-d916-404d-bc61-cdf77835da3b" />


---

### Verify Static Entries Are Gone

```bash
show ip nat translations
```

The NAT table should no longer contain static mappings.

<img width="431" height="65" alt="image" src="https://github.com/user-attachments/assets/e4c8003e-e60d-4dfb-b88c-ff845a409c57" />

---

## Task 1 – Create the Inside Address List

Dynamic NAT requires an ACL to identify which internal addresses are eligible for translation.

### Configure & Verify ACL 1

<img width="529" height="95" alt="image" src="https://github.com/user-attachments/assets/2d0aac72-3193-4fdd-890d-2bc14d658be6" />


This ACL matches every host within the Coffee House LAN.


---

## Task 2 – Create the Public Address Pool

Next, I created a pool of public IP addresses that Dynamic NAT can allocate to internal hosts.

### Configure NAT Pool

```bash
ip nat pool Cafe-Public 216.0.5.50 216.0.5.100 netmask 255.255.255.0
```

This creates a pool containing:

```text
216.0.5.50 - 216.0.5.100
```

Available public addresses:

```text
51 addresses
```

---

### Verify the Pool

```bash
show running-config | include ip nat pool
```

<img width="647" height="65" alt="image" src="https://github.com/user-attachments/assets/307e28bf-34b6-4c08-9757-4730d8cbcd9a" />

---

## Task 3 – Associate the ACL with the Pool

With the ACL and pool created, the final step was linking them together.

### Configure Dynamic NAT

```bash
ip nat inside source list 1 pool Cafe-Public
```

This tells the router:

> Any host matched by ACL 1 may borrow an address from the Cafe-Public pool.

---

### Verify Inside and Outside Interfaces

<img width="356" height="378" alt="image" src="https://github.com/user-attachments/assets/ba1b536b-7b56-4b1b-8a81-74050b24bf54" />

---

### Verify NAT Configuration

```bash
show running-config | section nat
```

<img width="604" height="93" alt="image" src="https://github.com/user-attachments/assets/1ec2b180-b51d-4072-b193-5a93da24b813" />


## Task 4 – Generate Traffic

To trigger Dynamic NAT translations, I generated traffic from both internal hosts.

### PC1

```bash
ping -c 5 1.1.1.1
```
<img width="493" height="198" alt="image" src="https://github.com/user-attachments/assets/5b8238bf-9a5f-404c-a43f-e1617eddbc50" />

---

### PC2

Test:

```bash
ping -c 5 1.1.1.1
```

<img width="497" height="198" alt="image" src="https://github.com/user-attachments/assets/4058dfda-6a48-4392-ac4a-04f8ef762af0" />


Both hosts successfully generated outbound traffic and requested public addresses from the NAT pool.

---

## Verify Dynamic NAT Translations

While traffic was active:

```bash
show ip nat translations
```

<img width="655" height="91" alt="image" src="https://github.com/user-attachments/assets/54e7a91a-3f2c-4ea5-8af6-0ca15ca7cf8a" />


Notice that each device temporarily received a different address from the configured pool.

Unlike Static NAT, these addresses are assigned dynamically and can change over time.

---

## Verify NAT Statistics

<img width="603" height="342" alt="image" src="https://github.com/user-attachments/assets/23bab1ff-29fe-4ce6-9114-846bbcb7db4c" />

Useful information includes:

- Active translations
- Pool utilization
- Available addresses
- Translation hits and misses

Example:

```text
Pool name: Cafe-Public

Addresses in pool: 51
Addresses allocated: 2
Addresses available: 49
```

---

## Verification Checklist

- [X] Static NAT entries removed
- [X] ACL 1 created
- [X] NAT pool created
- [X] ACL associated with pool
- [X] Inside interface configured
- [X] Outside interface configured
- [X] PC1 successfully translated
- [X] PC2 successfully translated
- [X] NAT table verified
- [X] NAT statistics verified
- [X] Configuration saved

---

## What I Practiced

- Dynamic NAT
- Standard ACLs
- NAT pools
- Translation verification
- NAT troubleshooting
- Pool allocation monitoring
- Connectivity testing

---

## Key Takeaways

- Dynamic NAT uses a pool of public IP addresses rather than permanent one-to-one mappings.
- Internal hosts borrow addresses from the pool when traffic is generated.
- ACLs determine which devices are eligible for translation.
- NAT pools define the available public address space.
- Dynamic NAT conserves public IP addresses compared to Static NAT.
- If all addresses in the pool are in use, additional hosts cannot be translated until an address becomes available.
- `show ip nat translations` and `show ip nat statistics` are essential troubleshooting commands when working with NAT.
