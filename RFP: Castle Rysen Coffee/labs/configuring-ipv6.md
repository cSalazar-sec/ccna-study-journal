# Configuring IPv6

Bring the Fallout Shelter online with IPv6. Every shelter VLAN should be using the same address plan as the cafe, link-local neighbors ready to communicate, and the uplink back to the Castle connected with static routes.

---

## Lab Objectives

* Prepare Castle-Fallout-RTR for IPv6 by confirming routing services and baseline connectivity.
* Assign the new `/64` overlays to the shelter VLAN interfaces using EUI-64 addressing.
* Configure the IPv6 uplink between Castle-Fallout-RTR and Castle-Cafe-RTR.
* Establish IPv6 reachability between the two sites using static routes.
* Verify connectivity between the cafe and shelter VLANs.

---

## Task 0 — Confirm the Shelter’s IPv6 Staging

Before configuring IPv6 addresses, make sure Castle-Fallout-RTR is ready to forward IPv6 traffic and that the required interfaces are operational.

Confirm that IPv6 routing is enabled.

<img width="315" height="39" alt="image" src="https://github.com/user-attachments/assets/a985923e-94fa-4e32-a5eb-8f8138de807e" />

In the lab, `Ethernet0/0` and `Ethernet0/1` were initially be administratively down, I turned them up.

<img width="711" height="316" alt="image" src="https://github.com/user-attachments/assets/e735c425-71a0-4c86-b491-9baf78b12891" />

---

## Task 1 — Configure the Shelter VLANs with EUI-64

Each shelter VLAN uses an Ethernet subinterface on Castle-Fallout-RTR.

The goal is to assign each VLAN its own `/64` IPv6 network while allowing the router to automatically generate the interface identifier using EUI-64.

### IPv6 Addressing Plan

| VLAN    | Description    | IPv6 Network        |
| ------- | -------------- | ------------------- |
| VLAN 10 | Admin          | `2001:DB8:1:4::/64` |
| VLAN 20 | Management     | `2001:DB8:1:5::/64` |
| VLAN 30 | Internal Video | `2001:DB8:1:6::/64` |
| VLAN 40 | Guest          | `2001:DB8:1:7::/64` |


<img width="611" height="148" alt="image" src="https://github.com/user-attachments/assets/4e1bcba2-e3b0-446a-aebc-2e5dbf590f6b" />

### Verification

Each shelter subinterface should have a global unicast address generated through EUI-64.

<img width="428" height="270" alt="image" src="https://github.com/user-attachments/assets/d948e685-5a31-423f-8bae-1f5c51a97735" />

---

## Task 2 — Configure the IPv6 Uplink to Castle-Cafe-RTR

The uplink between Castle-Fallout-RTR and Castle-Cafe-RTR uses `Ethernet0/1`.

### IPv6 Uplink

| Device             | Interface   | IPv6 Address         |
| ------------------ | ----------- | -------------------- |
| Castle-Cafe-RTR    | Ethernet0/1 | `2001:DB8:1:3::1/64` |
| Castle-Fallout-RTR | Ethernet0/1 | `2001:DB8:1:3::2/64` |

### On Castle-Fallout-RTR

<img width="527" height="38" alt="image" src="https://github.com/user-attachments/assets/79ee5fe3-3d40-495d-a0ac-f81456a503db" />

### On Castle-Cafe-RTR

<img width="499" height="37" alt="image" src="https://github.com/user-attachments/assets/89d8ec03-a5d6-4ff0-8df0-94ff99bd3732" />

### Verification

<img width="617" height="91" alt="image" src="https://github.com/user-attachments/assets/21e4e9a3-c5b5-4940-aadf-be4eab1d56c4" />

---

## Task 3 — Configure Static IPv6 Routes Between Sites

With the IPv6 addressing in place, I configured static routes so both routers know how to reach the remote networks.

The next-hop addresses are:

```text
Castle-Cafe-RTR     2001:DB8:1:3::1
Castle-Fallout-RTR  2001:DB8:1:3::2
```

### Castle-Fallout-RTR

Configure static routes toward the cafe networks:

| Destination         | Next Hop          |
| ------------------- | ----------------- |
| `2001:DB8:1:1::/64` | `2001:DB8:1:3::1` |
| `2001:DB8:1:2::/64` | `2001:DB8:1:3::1` |


<img width="612" height="38" alt="image" src="https://github.com/user-attachments/assets/65f4064c-8dde-41d3-baa1-1ac747344158" />

### Verification:

<img width="701" height="308" alt="image" src="https://github.com/user-attachments/assets/47894c0c-e04c-48a8-98c6-db38047bc9ae" />

---

### Castle-Cafe-RTR

Configure static routes toward the shelter networks:

| Destination         | Next Hop          |
| ------------------- | ----------------- |
| `2001:DB8:1:4::/64` | `2001:DB8:1:3::2` |
| `2001:DB8:1:5::/64` | `2001:DB8:1:3::2` |
| `2001:DB8:1:6::/64` | `2001:DB8:1:3::2` |
| `2001:DB8:1:7::/64` | `2001:DB8:1:3::2` |

<img width="588" height="71" alt="image" src="https://github.com/user-attachments/assets/8e719148-8c2b-4f2b-9e70-98d012756b3f" />

### Verification:

<img width="677" height="597" alt="image" src="https://github.com/user-attachments/assets/14292e24-5480-4a9a-a9a4-2fb0489d5ec5" />


---

## Task 4 — Verify IPv6 Connectivity

### Objective

Prove that the IPv6 addressing and static routes are working between the cafe and Fallout Shelter.

### Verification:

From Castle-Cafe-RTR

<img width="779" height="183" alt="image" src="https://github.com/user-attachments/assets/c09b0476-bef9-4acb-b7dc-e22c6a7f41a3" />

From Castle-Fallout-RTR

<img width="625" height="186" alt="image" src="https://github.com/user-attachments/assets/5d551072-a62b-424e-8615-982e44d03f06" />


* Castle-Fallout-RTR can reach the cafe IPv6 networks.
* Castle-Cafe-RTR can reach the shelter IPv6 networks.
* The IPv6 static routes are present in both routing tables.
* The shelter VLAN interfaces have EUI-64-generated global addresses.
* The `2001:DB8:1:3::/64` uplink is operational.
* Link-local addresses are present on the IPv6-enabled interfaces.

---

## Completion Check

* [X] IPv6 routing is enabled on Castle-Fallout-RTR.
* [X] `Ethernet0/0` and `Ethernet0/1` are operational.
* [X] VLAN 10 uses `2001:DB8:1:4::/64`.
* [X] VLAN 20 uses `2001:DB8:1:5::/64`.
* [X] VLAN 30 uses `2001:DB8:1:6::/64`.
* [X] VLAN 40 uses `2001:DB8:1:7::/64`.
* [X] EUI-64 addressing is configured on all shelter VLAN subinterfaces.
* [X] Castle-Fallout-RTR uses `2001:DB8:1:3::2/64`.
* [X] Castle-Cafe-RTR uses `2001:DB8:1:3::1/64`.
* [X] Static routes to the cafe networks are configured on Castle-Fallout-RTR.
* [X] Static routes to the shelter networks are configured on Castle-Cafe-RTR.
* [X] IPv6 connectivity has been verified in both directions.
