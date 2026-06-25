# Routing Between VLANs (Router-on-a-Stick)

## Scenario

The Coffee House network currently contains two separate VLANs:

- **VLAN 10 – Admin Network**
- **VLAN 20 – Patron Network**

Although both VLANs exist on the switch, devices in different VLANs cannot communicate because Layer 2 switches do not route traffic between VLANs.

To solve this, I implemented **Router-on-a-Stick (ROAS)** by converting the switch uplink into a trunk and creating VLAN-aware subinterfaces on the router.

This allows a single physical router interface to act as the default gateway for multiple VLANs. I also configured DHCP services for each subnet.

---

## Network Diagram

<img width="1050" height="850" alt="image" src="https://github.com/user-attachments/assets/5a9eab4e-2aa7-4eb0-963f-80e9d0c6b425" />

---

## Addressing Plan

### VLAN 10 – Admin

| Item | Value |
|--------|--------|
| VLAN ID | 10 |
| Network | 10.0.18.0/27 |
| Subnet Mask | 255.255.255.224 |
| Default Gateway | 10.0.18.1 |
| DHCP Pool | ADMIN-10 |

---

### VLAN 20 – Patron

| Item | Value |
|--------|--------|
| VLAN ID | 20 |
| Network | 10.0.18.32/27 |
| Subnet Mask | 255.255.255.224 |
| Default Gateway | 10.0.18.33 |
| DHCP Pool | PATRON-20 |

---

## Lab Objectives

By completing this lab I practiced:

- Configuring Router-on-a-Stick (ROAS)
- Creating 802.1Q trunks
- Building router subinterfaces
- Assigning VLAN gateways
- Configuring DHCP services
- Verifying trunk operation
- Testing inter-VLAN routing
- Troubleshooting VLAN connectivity

---

# Task 0 – Configure the Switch-to-Router Trunk

The connection between **Cafe-SW1** and **Cafe-RTR1** must carry traffic for multiple VLANs.

To accomplish this, I converted the switch uplink into a trunk port.

---

## Configure Ethernet0/0 as a Trunk

Initially I had to tell the switch how to mark data packets so so they can travel across a single cable and be sorted into the correct network.
---

```bash
interface ethernet0/0
 switchport trunk encapsulation do1q
 switchport mode trunk
```
<img width="460" height="31" alt="image" src="https://github.com/user-attachments/assets/eaf24a35-19e4-4422-96be-252688e2709b" />

---

## Verify Trunk Status

```bash
show interfaces trunk
```

Initially, the trunk may not appear because the router has not yet been configured to tag VLAN traffic.

This is expected.

---

## Expected Result After Router Configuration

```text
Port        Mode         Encapsulation  Status

Et0/0       on           802.1q         trunking
```

Allowed VLANs should include:

```text
10,20
```

---

# Task 1 – Configure Router-on-a-Stick

The router will provide Layer 3 connectivity for both VLANs using subinterfaces.

---

## Remove the Legacy IP Address

The physical interface must not contain an IP address when using ROAS.

<img width="683" height="140" alt="image" src="https://github.com/user-attachments/assets/5e847ef1-da9f-47fc-a0a3-67cbbcd8d38b" />

---

## Create VLAN 10 & 20 Subinterfaces

<img width="324" height="124" alt="image" src="https://github.com/user-attachments/assets/873d2804-0125-40ec-8289-01df403a4f92" />

These interfaces become the default gateway for VLAN 10 & 20.

---

## Verify Interface Status

<img width="640" height="91" alt="image" src="https://github.com/user-attachments/assets/f0911b46-b2b9-4349-93dc-cbc9ebabd8df" />

---

## Understanding ROAS

Instead of requiring one physical router interface per VLAN:

```text
VLAN 10 → Physical Interface
VLAN 20 → Physical Interface
```

ROAS allows:

```text
VLAN 10 → Ethernet0/0.10
VLAN 20 → Ethernet0/0.20
```

Both VLANs share a single physical connection while remaining logically separated.

---

# Task 2 – Configure DHCP Services

The router will provide DHCP addresses for both VLANs.

---

## Configure DHCP Exclusions

Reserve gateway addresses and any other infrastructure addresses.

### VLAN 10

```bash
ip dhcp excluded-address 10.0.18.1 10.0.18.5
```

### VLAN 20

```bash
ip dhcp excluded-address 10.0.18.33 10.0.18.36
```

---

## Create DHCP Pool for VLAN 10

<img width="495" height="67" alt="image" src="https://github.com/user-attachments/assets/5f3977ab-f5bd-48cc-8166-0a40b6cbd085" />

---

## Create DHCP Pool for VLAN 20

<img width="492" height="69" alt="image" src="https://github.com/user-attachments/assets/51c5d4ac-e115-45c4-8472-9fb19eb31106" />

---

## Verify DHCP Pools

<img width="683" height="395" alt="image" src="https://github.com/user-attachments/assets/19b00e42-f5ae-4080-8c86-3fdd09028024" />

---

---

## Verify DHCP Leases

<img width="810" height="124" alt="image" src="https://github.com/user-attachments/assets/6a71d8fc-9395-4c35-a4b1-a63aad326e08" />

---

# Verify Inter-VLAN Routing

At this point:

- VLAN 10 hosts know their gateway
- VLAN 20 hosts know their gateway
- Router subinterfaces route between networks

---

## Test from Admin VLAN

<img width="482" height="191" alt="image" src="https://github.com/user-attachments/assets/10ffaac5-40ce-42f6-a4a7-48e3aca59047" />

---

## Test from Patron VLAN

<img width="476" height="190" alt="image" src="https://github.com/user-attachments/assets/7f9f8337-c81e-4b94-ae57-3b7f0b5ff2a6" />

---

# Verification Checklist

- [X] Switch uplink configured as trunk
- [X] VLAN 10 active
- [X] VLAN 20 active
- [X] Router physical interface prepared for ROAS
- [X] Ethernet0/0.10 configured
- [X] Ethernet0/0.20 configured
- [X] Subinterfaces operational
- [X] DHCP exclusions configured
- [X] ADMIN-10 DHCP pool created
- [X] PATRON-20 DHCP pool created
- [X] Clients received DHCP addresses
- [X] Inter-VLAN routing verified
- [X] Configurations saved

---

# What I Practiced

- Router-on-a-Stick (ROAS)
- 802.1Q trunking
- Router subinterfaces
- DHCP configuration
- VLAN gateway assignment
- Inter-VLAN routing
- Connectivity verification
- Network troubleshooting

---

# Key Takeaways

- VLANs separate broadcast domains but do not provide routing.
- Inter-VLAN communication requires a Layer 3 device.
- Router-on-a-Stick uses a single physical interface with multiple subinterfaces.
- Each subinterface acts as the default gateway for its VLAN.
- Trunk links carry multiple VLANs across a single connection using 802.1Q tags.
- DHCP pools can be configured directly on the router for each VLAN.
- Successful inter-VLAN communication confirms both trunking and routing are functioning correctly.
