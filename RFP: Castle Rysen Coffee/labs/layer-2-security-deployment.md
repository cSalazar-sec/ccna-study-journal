# Layer 2 Security Deployment

## Overview

In this lab, port security, DHCP snooping, and Dynamic ARP Inspection (DAI) are configured on the cafe switches.

The goal is to protect the access layer by limiting the number of devices that can connect to an access port, allowing the switches to identify legitimate DHCP bindings, and using those bindings to validate ARP traffic.

## Objectives

* Configure port security on the cafe access ports.
* Limit patron ports to one device.
* Use sticky MAC learning only on the administrative endpoints.
* Enable DHCP snooping for VLANs 1, 10, and 20.
* Configure trusted DHCP interfaces and rate limits.
* Enable Dynamic ARP Inspection (DAI).
* Configure the appropriate DAI trust boundaries.
* Verify the security configuration and test connectivity.

---
## Network Diagram

<img width="1656" height="950" alt="image" src="https://github.com/user-attachments/assets/57c59126-fd74-4db8-95b9-02a337286e3a" />

---

# Task 1 — Configure Port Security on Cafe-01-SW1

Configured the access ports on `Cafe-01-SW1` so patron ports allow only one device.

Administrative ports will also be limited to one device, but sticky MAC learning will be enabled so the switch can dynamically learn and remember the authorized MAC address.


Enabled port security and limited each port to one MAC address.
Made sure sticky MAC learning is disabled on the patron ports.
Enabled sticky MAC learning on admin interfaces.

<img width="324" height="413" alt="Captura de pantalla 2026-08-16 181550" src="https://github.com/user-attachments/assets/f57d4599-efef-4388-9ef3-68b4f5f02d01" />


### Results

* `Ethernet0/3` has port security enabled.
* The maximum number of secure MAC addresses is `1`.
* Sticky MAC learning is enabled on `Ethernet0/3`.
* Patron-facing ports have a maximum of one MAC address.
* The infrastructure-facing ports remain without port security.

> `Security Action: Shutdown` is the default violation action. It does not mean the port is currently shut down.

---

# Task 2 — Configure Port Security on Cafe-01-SW2

Apply the same security policy to `Cafe-01-SW2`.

Patron-facing ports should allow only one device, while the Plex administrative port should use sticky MAC learning.

<img width="661" height="465" alt="image" src="https://github.com/user-attachments/assets/80d0116a-7ada-4d77-b8b6-2696541c6827" />


### Results

* `Ethernet1/0` has sticky MAC learning enabled.
* `Ethernet0/2` has port security enabled with a maximum of one MAC address.
* Patron-facing ports are limited to one device.
* No security violations have occurred.

---

# Task 3 — Configure DHCP Snooping

DHCP snooping will prevent unauthorized DHCP servers from responding to clients.

The router-facing and infrastructure-facing interfaces are trusted, while normal access ports remain untrusted.

### Cafe-01-SW1

Configure DHCP snooping for VLANs 1, 10, and 20.

<img width="412" height="95" alt="image" src="https://github.com/user-attachments/assets/dd53ed27-a2b0-400f-83e3-64c505e1615c" />

### Trusted interface

interface Ethernet6/0
 switchport trunk encapsulation dot1q
 switchport mode trunk
 ip dhcp snooping trust

Limit Rate

<img width="591" height="599" alt="image" src="https://github.com/user-attachments/assets/e9818fd0-fc39-4bb3-9667-b2f53e79370d" />

### Cafe-01-SW2

<img width="605" height="455" alt="image" src="https://github.com/user-attachments/assets/aa4cff40-fa19-48bc-88ca-3882490a6d5c" />


### Results

The clients should be able to obtain DHCP addresses from the router.

For this lab:

* `Cafe-01-PC` should receive an address from `10.1.20.0/24.`
  
  <img width="598" height="314" alt="image" src="https://github.com/user-attachments/assets/3ed2b239-08b8-4aec-89d9-012dd332e7a9" />

* `Cafe-01-Plex` should receive an address from `10.1.10.0/24`.

<img width="606" height="200" alt="image" src="https://github.com/user-attachments/assets/b71e6ee9-014f-49ca-8999-bb0441ee4242" />


* `Cafe-Edge-R1` should show the DHCP leases.

<img width="871" height="134" alt="image" src="https://github.com/user-attachments/assets/ebbfafd7-2f7f-468a-8a08-38af6e97ec2a" />

The DHCP snooping binding table may remain empty in the live lab image even though DHCP is working. If that happens, document it as a lab/simulator limitation rather than treating it as a configuration failure.

---

# Task 4 — Configure Dynamic ARP Inspection

Dynamic ARP Inspection uses the DHCP snooping database to determine whether ARP messages are legitimate.

Trusted infrastructure interfaces are allowed to pass ARP traffic, while access ports remain untrusted.

### Cafe-01-SW1

<img width="565" height="579" alt="image" src="https://github.com/user-attachments/assets/4b363444-87b4-4202-9fa3-05c44cc00c91" />


### Cafe-01-SW2

<img width="542" height="577" alt="image" src="https://github.com/user-attachments/assets/c9dd2aa2-7a5a-4d3d-b118-a85233afa347" />

---

# Task 5 — Test DHCP and ARP Inspection

Generate traffic from the cafe hosts and verify that DHCP and ARP inspection are operating.

---

# Expected Results

* `Cafe-01-PC` receives an address from the `10.1.20.0/24` subnet.
* `Cafe-01-Plex` receives an address from the `10.1.10.0/24` subnet.
* DHCP leases appear on `Cafe-Edge-R1`.
* The Plex host should be able to reach its gateway.
* DAI statistics show the switch inspecting ARP traffic.
* Trusted interfaces are identified correctly.
* Patron access ports remain untrusted.

In the current lab image, the DHCP snooping binding table remained empty. Because DAI depends on those bindings, ARP traffic from the patron VLAN was be dropped even though the DHCP process itself succeeds.

---

# Completion Check

Before considering the lab complete, verify:

* [X] Patron access ports are limited to one device.
* [X] Administrative ports use sticky MAC learning where required.
* [X] Infrastructure-facing links remain exempt from port security.
* [X] DHCP snooping is enabled for VLANs 1, 10, and 20.
* [X] DHCP trusted interfaces are configured correctly.
* [X] DHCP rate limits are applied to access ports.
* [X] Dynamic ARP Inspection is enabled for VLANs 1, 10, and 20.
* [X] DAI trust boundaries match the DHCP snooping trust boundaries.
* [X] DHCP clients receive valid addresses.
* [X] Gateway connectivity has been tested.
* [X] Port security, DHCP snooping, and DAI verification commands have been captured.
* [X] Any simulator limitations or unexpected behavior have been documented.
* [X] The final configuration has been saved.

---

# Key Takeaways

**Port Security** controls how many MAC addresses can appear on an access port and can use sticky learning to dynamically save authorized MAC addresses.

**DHCP Snooping** identifies trusted DHCP infrastructure and builds a binding between a client's IP address, MAC address, VLAN, and switchport.

**Dynamic ARP Inspection** uses those DHCP snooping bindings to determine whether ARP messages should be allowed or dropped.

Together, these features provide multiple layers of Layer 2 protection:

`Port Security → DHCP Snooping → Dynamic ARP Inspection`

The important part is that these features work together. DHCP snooping provides the information DAI uses to validate ARP traffic, while port security prevents an access port from simply accepting multiple unknown devices.
