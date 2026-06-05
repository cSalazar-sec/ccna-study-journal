# Coffee House ↔ Fallout Local Routing

## Scenario

The Coffee House network was connected to the Fallout Shelter network so both sites could exchange services before the main WAN cutover.

For this deployment I needed to:

- Verify both routers met Castle security standards
- Confirm IOS XE Version 17.16
- Bring the LAN gateway interfaces online
- Configure the point-to-point link between the routers
- Configure static routes for remote network reachability
- Verify end-to-end connectivity
- Save and document the final configuration

---

## Network Diagram

<img width="1360" height="943" alt="image" src="https://github.com/user-attachments/assets/62503946-bc1b-43e9-b7f4-0917d44a7e22" />

---

## Addressing Plan

| Device | Interface | IP Address |
|----------|------------|------------|
| Cafe-RT1 | Ethernet0/0 | 192.168.42.1/24 |
| Cafe-RT1 | Ethernet0/1 | 10.8.0.1/30 |
| Fallout-RT1 | Ethernet0/0 | 192.168.84.1/24 |
| Fallout-RT1 | Ethernet0/1 | 10.8.0.2/30 |

---

## Task 1 – Verify the Router Baseline

Before making any changes I verified the security posture of both routers.

Things verified:

- Correct hostname
- IOS XE Version 17.16
- Type 9 enable secret
- Local console authentication
- Local VTY authentication
- Login auditing enabled
- Existing interface descriptions preserved

Useful commands:

```bash
show version
show running-config
show interfaces description
show ip interface brief
```

I also captured the operational state of Ethernet0/0 and Ethernet0/1 before making any changes.

<img width="689" height="74" alt="image" src="https://github.com/user-attachments/assets/40b0d51e-f7cd-49af-b512-2e8f8066994f" />

<img width="680" height="74" alt="image" src="https://github.com/user-attachments/assets/d244ed1f-c6be-4d13-8745-d52d2304600c" />

---

## Task 2 – Configure the LAN Gateways

The next step was bringing the LAN interfaces online and assigning the gateway addresses.

### Cafe-RT1

```bash
interface ethernet0/0
 ip address 192.168.42.1 255.255.255.0
 no shutdown
```

### Fallout-RT1

```bash
interface ethernet0/0
 ip address 192.168.84.1 255.255.255.0
 no shutdown
```

After configuration I verified both interfaces were operational.

```bash
show ip interface brief
```

<img width="685" height="56" alt="image" src="https://github.com/user-attachments/assets/88fc42d9-7183-4dea-aed5-30968c54b5ab" />

<img width="681" height="72" alt="image" src="https://github.com/user-attachments/assets/6829d871-00ab-4bbe-be48-d7edb590a7f4" />

I also checked interface statistics before and after activation to confirm there were no errors or drops.

```bash
show interfaces ethernet0/0
```
Before activation

<img width="609" height="200" alt="image" src="https://github.com/user-attachments/assets/3733417e-a4fe-469b-ac6c-78993c9f2520" />


After activation

<img width="612" height="200" alt="image" src="https://github.com/user-attachments/assets/1230ab95-0e15-4428-b6fc-2d85d737ae5a" />


---

## Task 3 – Configure the Point-to-Point Link

To allow communication between the two sites, I configured a dedicated routed link between both routers.

### Cafe-RT1

```bash
interface ethernet0/1
 ip address 10.8.0.1 255.255.255.252
 no shutdown
```

### Fallout-RT1

```bash
interface ethernet0/1
 ip address 10.8.0.2 255.255.255.252
 no shutdown
```

After configuration was done, I then tested connectivity across the point-to-point segment.

From Cafe-RT1:

```bash
ping 10.8.0.2
```
<img width="583" height="89" alt="image" src="https://github.com/user-attachments/assets/cd9737cb-dec2-4112-a78d-35bdb5044902" />


From Fallout-RT1:

```bash
ping 10.8.0.1
```
<img width="562" height="72" alt="image" src="https://github.com/user-attachments/assets/81d0961a-2f3a-43ac-83da-5d6e605a0aa1" />

Successful replies confirmed Layer 3 connectivity between the routers.

---

## Task 4 – Configure Static Routing

With the point-to-point link operational, the next step was providing reachability to the remote LANs.

### Cafe-RT1

```bash
ip route 192.168.84.0 255.255.255.0 10.8.0.2
```

This route tells Cafe-RT1:

> To reach the Fallout LAN, send traffic to Fallout-RT1.

### Fallout-RT1

```bash
ip route 192.168.42.0 255.255.255.0 10.8.0.1
```

This route tells Fallout-RT1:

> To reach the Coffee House LAN, send traffic to Cafe-RT1.

I verified the routes were installed successfully.

```bash
show ip route
```

Expected output:

```text
S    192.168.84.0/24 via 10.8.0.2
S    192.168.42.0/24 via 10.8.0.1
```
<img width="468" height="39" alt="image" src="https://github.com/user-attachments/assets/79201c01-1168-4167-bd7a-eb427e8f0853" />

<img width="453" height="41" alt="image" src="https://github.com/user-attachments/assets/8bee2f1f-5611-47b7-8c50-9c3673cbf302" />

---

## Task 5 – Verify Connectivity

After configuring the static routes I tested communication between both networks.

From Cafe-RT1:

```bash
ping 192.168.84.1
```
<img width="593" height="89" alt="image" src="https://github.com/user-attachments/assets/74beb98d-7713-4c77-ad06-82f2f15db682" />

From Fallout-RT1:

```bash
ping 192.168.42.1
```
<img width="603" height="93" alt="image" src="https://github.com/user-attachments/assets/cdb60408-aef2-412c-8d00-15cf849a78d7" />

I also verified neighbor discovery using ARP.

```bash
show arp
```

Successful ARP entries confirmed the routers were learning each other's addresses correctly.

<img width="616" height="91" alt="image" src="https://github.com/user-attachments/assets/fb95a22a-2d89-40cd-a581-7891566c7630" />

<img width="631" height="95" alt="image" src="https://github.com/user-attachments/assets/07261467-1299-446f-953b-b6ab86cdb07e" />


---

## Task 6 – Final Validation

Before closing the change I performed a final health check.

Commands used:

```bash
show ip interface brief
show interfaces ethernet0/0
show interfaces ethernet0/1
show ip route
show arp
show interfaces description
```

Things verified:

- All interfaces were up/up
- No input or output errors
- No packet drops
- Static routes installed correctly
- ARP entries present
- Interface descriptions preserved

---

## Save Configuration

Once everything was validated, I saved the configuration on both routers.

```bash
copy running-config startup-config
```

or

```bash
write memory
```

---

## Verification Checklist

- [X] IOS XE Version 17.16 verified
- [X] Type 9 secret verified
- [X] Console authentication verified
- [X] VTY authentication verified
- [X] Login auditing verified
- [X] Cafe-RT1 LAN gateway configured
- [X] Fallout-RT1 LAN gateway configured
- [X] Point-to-point link operational
- [X] Static routes configured
- [X] Remote gateways reachable
- [X] ARP entries learned
- [X] Interface descriptions preserved
- [X] Configuration saved

---

## What I Practiced

- Router baseline verification
- Interface configuration
- Point-to-point addressing
- Static routing
- Routing table verification
- ARP verification
- Connectivity testing
- Network documentation
- Change validation and closeout

---

## Key Takeaways

- Static routes allow routers to reach networks that are not directly connected.
- Point-to-point links are commonly used to connect routers together.
- A router must have a matching route in its routing table before it can forward traffic.
- Verifying interface health is just as important as verifying connectivity.
- ARP tables provide useful information when validating neighbor reachability.
- Preserving interface descriptions is an important operational best practice.
- Saving the configuration ensures changes survive a reboot.
