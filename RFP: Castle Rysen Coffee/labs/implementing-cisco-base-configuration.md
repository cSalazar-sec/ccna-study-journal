# Implementing the Cisco Base Configuration

---

# Lab Overview

This lab focuses on implementing a standardized Cisco base configuration for the Castle Rysen infrastructure environment.

The objective is to establish:
- Consistent device naming
- Secure administrative access
- Remote management connectivity
- Hardened console and VTY access
- Persistent configuration storage

This foundational configuration acts as the deployment baseline for all future network devices within the environment.

---

# Scenario Briefing

> We’re standardizing the Castle Rysen base config today — naming, banners, hardened access, and a live management SVI. Get this template locked in here in the command bunker and we can clone it to every shelter before the next dust storm hits.

---

# Training Objectives

By completing this lab, you will:

- Configure device hostname conventions
- Deploy legal warning banners
- Secure privileged EXEC access
- Secure console and VTY sessions
- Configure password encryption
- Enable SSH-only remote access
- Configure a management SVI
- Label important interfaces
- Save configurations

---

# Network Diagram

<img width="1359" height="1157" alt="image" src="https://github.com/user-attachments/assets/c0b0b5da-2e98-4443-9b7e-e6715a8b1371" />


---

# Task 1 — Brand the Hardware

Rename the switch using the Castle Rysen naming convention so operational teams can quickly identify the device.

---

## Objective

Configure the hostname:

```text
Cafe-01-SW1
```

---

## Configuration Steps

### Enter Privileged EXEC Mode

```bash
enable
```

### Enter Global Configuration Mode

```bash
configure terminal
```

### Configure the Hostname

```bash
hostname Cafe-01-SW1
```

---

## Verification

The CLI prompt should now display:

<img width="309" height="37" alt="image" src="https://github.com/user-attachments/assets/e97d9af5-a959-4916-bfb6-e89e839776b4" />

---

# Task 2 — Configure the Warning Banner

Deploy the required legal warning banner to discourage unauthorized access.

---

## Objective

Configure the following MOTD banner:

```text
Unauthorized access ends badly. Authorized Castle Rysen engineers only.
```

---

## Configuration Steps

### Enter Banner Configuration

```bash
banner motd #Unauthorized access ends badly. Authorized Castle Rysen engineers only.#
```

---

## Verification Commands

```bash
show banner motd
```

or:

```bash
show running-config | include banner
```
<img width="567" height="36" alt="image" src="https://github.com/user-attachments/assets/230a637a-580c-4d80-a23d-ccee736327c9" />

---

# Task 3 — Secure Privileged EXEC Access

Protect privileged EXEC mode using an encrypted enable secret.

---

## Objective

Configure the enable secret:

```text
C4stleRysen!
```

---

## Configuration Steps

### Configure the Enable Secret

```bash
enable secret C4stleRysen!
```

---

## Verification Steps

Exit privileged mode:

```bash
disable
```

Re-enter privileged EXEC:

```bash
enable
```

The switch should prompt for the secret password.

<img width="141" height="54" alt="image" src="https://github.com/user-attachments/assets/2b5d8f62-d881-4e0c-a4dc-edeaabf61fde" />


---

# Task 4 — Secure the Console Line

Protect physical console access with authentication.

---

## Objective

Configure the console password:

```text
VaultAccess
```

---

## Configuration Steps

### Enter Console Line Configuration

```bash
line console 0
```

### Configure the Password

```bash
password VaultAccess
```

### Require Login Authentication

```bash
login
```

---

## Verification

Upon reconnecting to the console session, the switch should require authentication before granting access.

<img width="145" height="59" alt="image" src="https://github.com/user-attachments/assets/cbd9664a-517a-4fcb-a7b9-2a764f00535a" />


---

# Task 5 — Harden Remote Management Access

Configure secure remote administrative access.

---

## Objectives

- Enable password encryption
- Configure VTY authentication
- Restrict remote access to SSH only

---

## Configuration Steps

### Enable Password Encryption

```bash
service password-encryption
```

### Enter VTY Configuration

```bash
line vty 0 4
```

### Configure the VTY Password

```bash
password ShelterAccess
```

### Require Authentication

```bash
login
```

### Restrict Remote Access to SSH

```bash
transport input ssh
```
## Verification

<img width="329" height="91" alt="image" src="https://github.com/user-attachments/assets/e0ffd838-6a51-409d-a9a7-86677825a3cf" />


---

# Why SSH Matters

SSH provides:
- Encrypted remote management
- Secure authentication
- Protection against plaintext credential exposure

This is significantly more secure than Telnet.

## SSH Remote Access 

<img width="508" height="93" alt="image" src="https://github.com/user-attachments/assets/edf76156-491e-44c1-a358-94faa90deae3" />

---

# Task 6 — Configure the Management SVI

Bring the management interface online for remote administration.

---

## Objective

Configure VLAN 1 with:

```text
192.168.10.10/24
```

---

## Configuration Steps

### Enter VLAN Interface Configuration

```bash
interface vlan 1
```

### Configure the IP Address

```bash
ip address 192.168.10.10 255.255.255.0
```

### Enable the Interface

```bash
no shutdown
```

---

## Verification Command

```bash
show ip interface brief
```
---

## Expected Result

The SVI should eventually display:

```text
up up
```
<img width="636" height="107" alt="image" src="https://github.com/user-attachments/assets/bb018ded-6bde-4a21-b8da-bcc2918f9232" />

meaning:
- Layer 1 status is operational
- Layer 2 status is operational

---

# Task 7 — Label the Uplink Interface

Document critical infrastructure connections using interface descriptions.

---

## Objective

Configure a description on:

```text
Ethernet0/0
```

---

## Configuration Steps

### Enter Interface Configuration

```bash
interface ethernet0/0
```

### Configure the Description

```bash
description Uplink-to-Core-Distribution
```
<img width="654" height="56" alt="image" src="https://github.com/user-attachments/assets/68923a3d-a5a8-4fe1-a317-e376e794da9b" />

---

# Why Interface Descriptions Matter

Good interface descriptions:
- Improve troubleshooting
- Simplify operational support
- Help future engineers understand topology relationships

Documentation inside the configuration matters.

---

# Task 8 — Save the Configuration

Persist the running configuration so it survives a reboot or power failure.

---

## Configuration Steps

### Save the Configuration

```bash
copy running-config startup-config
```

Alternative command:

```bash
write memory
```

---

## Verification Command

```bash
show startup-config
```

---

# Key Takeaways

- Standardized configurations improve operational consistency
- Secure administrative access is critical
- SSH should always be preferred over Telnet
- Management SVIs allow remote device administration
- Interface descriptions improve maintainability
- Configurations must be saved to survive reloads
- Base configurations form the foundation of secure infrastructure deployments
