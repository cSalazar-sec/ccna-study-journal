# ACL Deployment

## Overview

The Coffee House network is fully operational, but not every device should have unrestricted access to internal resources.

In this lab, I configured **Extended and Standard Access Control Lists (ACLs)** to secure the network. The objective is to allow patrons access only to the Plex media server(specific ports), prevent access to the rest of the administrative network, and restrict management access so only trusted networks can remotely administer network devices.

---

# Objectives

- Document the addressing used by the Patron, Admin, and Management networks.
- Configure a named Extended ACL to control traffic between VLANs.
- Allow patrons to access only the required Plex services.
- Block all other Patron-to-Admin communication.
- Preserve connectivity to all other destinations.
- Configure a Standard ACL to restrict Telnet and SSH access.
- Apply management restrictions to both the router and switch.
- Validate ACL functionality using connectivity tests and hit counters.

---

# Network Diagram

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/991abc9a-35df-4bd3-9b68-0923294bdf4b" />

---

# Task 1 — Review the Addressing Plan

Before creating any ACLs, identify the subnets and devices that will be referenced in the security policy.

- The Admin VLAN subnet & Patron VLAN subnet
  
<img width="348" height="168" alt="image" src="https://github.com/user-attachments/assets/a2cecc05-e117-4e00-a94b-b30f7b885965" />

- The Fallout Core Management subnet
  
<img width="350" height="60" alt="image" src="https://github.com/user-attachments/assets/789471e0-cf51-49c2-8cf3-08d6628a1b2c" />

- The IP address of the Plex server
<img width="609" height="56" alt="image" src="https://github.com/user-attachments/assets/325fdfe7-2033-4bab-b124-ede20effa2dc" />

- The Router-on-a-Stick subinterfaces
<img width="304" height="184" alt="image" src="https://github.com/user-attachments/assets/839c70f2-a7b8-4511-b495-345217a24e28" />

Also identified every TCP and UDP port required for Plex access.

TCP 443, 32400, 32469 and UDP 1900, 5353

---

# Task 2 — Configure the Extended ACL

Create a named Extended ACL that enforces the following policy:

- Permit Patron VLAN hosts to access the Plex server using only the required TCP and UDP ports.
- Deny all remaining Patron-to-Admin traffic.
- Permit all other traffic so Internet and external communication continue functioning normally.

<img width="972" height="163" alt="image" src="https://github.com/user-attachments/assets/d4eaf1ff-7341-4f1a-9d35-f3782a0dba33" />

---

# Task 3 — Apply and Test the ACL

Apply the ACL inbound on the Patron VLAN gateway interface.

<img width="526" height="40" alt="image" src="https://github.com/user-attachments/assets/ab0687ae-e3c1-4d0a-a29b-6e6b38694181" />

### Verify

From a Patron workstation:

- Confirmed Plex traffic succeeds.
- Verified normal communication to other Admin devices is denied.
- Confirmed Internet connectivity still works.

Review the ACL counters to verify packets are matching the expected entries.

<img width="666" height="163" alt="image" src="https://github.com/user-attachments/assets/6f832e95-73da-41a4-9f31-52a00bb1cb7d" />

---

# Task 4 — Secure Remote Management

Create a named Standard ACL that permits remote management only from:

<img width="619" height="92" alt="image" src="https://github.com/user-attachments/assets/8ab934d1-8838-4446-b08e-3ec384ec0dc2" />

All other networks should be denied by the implicit deny.

### Apply the ACL

Configure the ACL on the VTY lines:

<img width="520" height="75" alt="image" src="https://github.com/user-attachments/assets/6135bea3-bf7d-4cba-be4d-d0a64dbde8a2" />

---

# Validation

- Authorized Admin workstations can establish Telnet or SSH sessions.
- Authorized Fallout management hosts can establish Telnet or SSH sessions.
  
<img width="341" height="203" alt="image" src="https://github.com/user-attachments/assets/45f1ee9f-7d92-4533-ae89-f8de8ea55c2a" />

- Patron devices cannot remotely access either network device.

<img width="409" height="41" alt="image" src="https://github.com/user-attachments/assets/0f1a00da-54d2-4e18-ac1c-0ec5f3334b06" />

---

# Key Takeaways

- **Standard ACLs** filter traffic using only the **source IP address** and are commonly used to secure management access.

- **Extended ACLs** filter using:
  - Source IP address
  - Destination IP address
  - Protocol
  - Port numbers

- ACLs are processed **from top to bottom**, and the **first matching statement wins**.

- Every ACL ends with an **implicit deny**, even though it does not appear in the configuration.

- Place **Extended ACLs** as close to the traffic source as practical.

- Place **Standard ACLs** as close to the destination as practical.

- ACL hit counters provide an easy way to verify that traffic is matching the intended rules.

- Named ACLs are generally preferred over numbered ACLs because they are easier to read, maintain, and modify.

- Always finish an Extended ACL with an appropriate permit statement if other traffic should continue flowing. Otherwise, the implicit deny will block everything that wasn't explicitly permitted.
