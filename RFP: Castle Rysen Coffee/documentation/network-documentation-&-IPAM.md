# Network Documentation & IPAM

## What is IPAM?

IPAM stands for **IP Address Management**.

It's the process of tracking:

- IP addresses
- Subnets
- Device assignments
- Network infrastructure
- Hardware inventory
- Firmware versions
- Warranty information
- Lifecycle information

As networks grow, good documentation becomes just as important as good configuration. A poorly documented network quickly becomes difficult to troubleshoot, maintain, and scale.

The goal of IPAM is simple:

> Know what every device is, where it is, what address it uses, and who is responsible for it.

---

## Why Documentation Matters

Good documentation helps answer questions like:

- What device owns this IP address?
- What switch is this endpoint connected to?
- Which router is acting as the default gateway?
- Is this hardware still under warranty?
- What firmware version is currently installed?
- When was this device deployed?

Without documentation, every troubleshooting session becomes an investigation.

With documentation, most answers are only a few clicks away.

---

## Device Inventory

| Device Name | Model | Serial Number | Purchase Date | In-Service Date | Warranty Expiration | Firmware Version |
|-------------|---------|----------------|----------------|-----------------|--------------------|------------------|
| cafe01-sw01 | WS-C2960-24TT-L | FOC1010X104 | TBD | TBD | TBD | Version 15.0(2)SE4 |
| cafe01-sw02 | WS-C2960-24TT-L | FOC1010X104 | TBD | TBD | TBD | Version 15.0(2)SE4 |
| cafe01-RT01 | CISCO1941/K9 | FTX1524FO82 | TBD | TBD | TBD | Version 15.1(4)M4 |
| fallout01-RT01 | CISCO1941/K9 | FTX1524WASR | TBD | TBD | TBD | Version 15.1(4)M4 |

---

## IP Address Inventory

| Device Name | Interface | MAC Address | IP Address |
|-------------|------------|-------------|------------|
| cafe01-sw01 | VLAN1 | 00e0.a33e.151b | 192.168.1.10/24 |
| cafe01-sw02 | VLAN1 | 0002.172e.d4b8 | 192.168.1.11/24 |
| cafe01-RT01 | GigabitEthernet0/0 | 0001.42aa.2101 | 192.168.1.1/24 |
| cafe01-RT01 | GigabitEthernet0/0/0 | 00d0.d30d.56db | 192.168.2.1/24 |
| cafe01-RT01 | GigabitEthernet0/1 | 0001.42aa.2102 | 216.0.5.2/24 |
| fallout01-RT01 | GigabitEthernet0/0 | 0001.c924.8601 | 192.168.3.1/24 |
| fallout01-RT01 | GigabitEthernet0/0/0 | 00e0.a315.dbc3 | 192.168.2.2/24 |

---

## Network Summary

| Network | Purpose |
|----------|----------|
| 192.168.1.0/24 | Coffee House LAN |
| 192.168.2.0/24 | WAN Network |
| 192.168.3.0/24 | Fallout Shelter LAN |
| 216.0.5.0/24 | ISP / Internet Connection |

---

## Network Diagram

<img width="1639" height="959" alt="image" src="https://github.com/user-attachments/assets/97069260-4758-48f2-ae0f-a10571737438" />


---


## Documentation Best Practices

When creating network documentation:

### Use Consistent Naming

Examples:

```text
cafe01-sw01
cafe01-sw02
cafe01-rt01
fallout01-rt01
```

A consistent naming convention makes devices easier to identify and locate.

---

### Track Lifecycle Information

Always document:

- Purchase date
- In-service date
- Warranty expiration
- Firmware version

This information becomes extremely valuable during upgrades, audits, and hardware replacement projects.

---

### Keep Documentation Current

Documentation is only useful if it's accurate.

Whenever a change is made:

- Update IP assignments
- Update interface descriptions
- Record hardware replacements
- Document firmware upgrades

---

### Make Documentation Easy to Read

If you want documentation to survive more than a week, make it easy to update and pleasant to look at.

Ugly, cluttered documentation gets ignored fast.

Use:

- Clear headers
- Consistent formatting
- Descriptive column names
- Logical organization

The easier documentation is to maintain, the more likely it is to stay accurate.

---

## What I Practiced

- Network documentation
- IP Address Management (IPAM)
- Device inventory management
- Asset tracking
- MAC address documentation
- Interface inventory creation
- Network lifecycle management

---

## Key Takeaways

- IPAM is the process of tracking IP addresses and device assignments throughout a network.
- Good documentation reduces troubleshooting time and operational risk.
- Hardware inventory should include lifecycle information such as purchase date, warranty status, and firmware versions.
- Consistent naming conventions make networks easier to manage.
- Documentation should be treated as part of the network, not an afterthought.
- The best documentation is accurate, organized, and easy to update.
