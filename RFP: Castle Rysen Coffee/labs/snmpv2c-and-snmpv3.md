# SNMPv2c and SNMPv3 Configuration

## Scenario

To ensure effective monitoring and management, RTR-Training-01 must be configured for SNMP polling while maintaining strict access controls and security parameters.

This lab demonstrates establishing controlled SNMPv2c access using standard Access Control Lists (ACLs) to restrict polling to an authorized management station. Additionally, it implements secure SNMPv3 with authentication (SHA) and encryption (AES-128) to ensure modern, encrypted telemetry collection.

---

## Addressing Plan

| Device | Interface | IP Address |
| --- | --- | --- |
| RTR-Training-01 | Ethernet0/0 | 10.23.0.1/24 |
| Management Station | Ethernet0/0 | 10.23.0.50/24 |

---

## Lab Objectives

* Confirm RTR-Training-01 connectivity and initial SNMP configuration posture
* Configure a standard access list (`SNMP-MGMT`) restricting access to host 10.23.0.50
* Publish SNMPv2c read-only (`CSTL-RO`) and read-write (`CSTL-RW`) community strings bound to the ACL
* Update system contact and location details for record keeping
* Create an SNMPv3 group (`CastleSecure`) enforcing authentication and privacy (`priv`)
* Provision an SNMPv3 user (`ChrisOps`) utilizing SHA-1 authentication and AES-128 encryption
* Validate SNMPv2c communities, SNMPv3 group/user parameters, and SNMP agent statistics

---

## Task 0 – Reachability and Initial Posture Check

Before making configuration changes, verify reachability to the Ethernet0/0 interface and review the current SNMP configuration state.

### Verify Interface and Current SNMP Configuration

<img width="738" height="257" alt="image" src="https://github.com/user-attachments/assets/6a0cb2b7-3281-4bdb-b927-77725a1c1cb7" />

---

## Task 1 – Publish Controlled SNMPv2c Communities

Configure approved SNMPv2c community strings bound to Castle Rysen’s management ACL so authorized tools can collect telemetry securely.

### Step 1: Create the Management ACL

> A standard ACL limits SNMP polling exclusively to host 10.23.0.50, dropping all unauthorized origins.

<img width="500" height="35" alt="image" src="https://github.com/user-attachments/assets/63e7baaf-1765-41f3-826e-af3217c3e78d" />

---

### Step 2: Define SNMP Communities and System Information

<img width="581" height="41" alt="image" src="https://github.com/user-attachments/assets/53b57448-e1b3-4da0-ab98-9da68317b472" />


Communities created

<img width="531" height="217" alt="image" src="https://github.com/user-attachments/assets/5bfb66b9-a341-45f6-9a04-c0a5beafdecf" />

---


## Task 2 – Secure SNMPv3 Authentication and Privacy

Require Castle’s management platform to authenticate and encrypt every session by standing up an SNMPv3 group and user tied to a hardened policy.

### Step 1: Create the SNMPv3 Group

> The `priv` keyword enforces both authentication and privacy (encryption) for all users belonging to the group.

```bash
snmp-server group CastleSecure v3 priv

```

<img width="569" height="42" alt="image" src="https://github.com/user-attachments/assets/c50c4078-323f-487a-bd91-d760dce53b04" />

---

### Step 2: Configure the SNMPv3 User

```bash
snmp-server user ChrisOps CastleSecure v3 auth sha P@SSw0rd!23 priv aes 128 EncP@ss!23

```

> The authentication and privacy passwords are case sensitive and must match on the management station.

<img width="421" height="152" alt="image" src="https://github.com/user-attachments/assets/0c638183-7e7c-4cce-9384-f54bfb8e05ba" />

---

## Verification Checklist

* [X] Ethernet0/0 verified up/up with 10.23.0.1/24 address
* [X] Standard ACL `SNMP-MGMT` created permitting 10.23.0.50
* [X] Community `CSTL-RO` (Read-Only) configured and bound to ACL `SNMP-MGMT`
* [X] Community `CSTL-RW` (Read-Write) configured and bound to ACL `SNMP-MGMT`
* [X] System contact and location fields configured
* [X] SNMPv3 group `CastleSecure` created enforcing `priv` security model
* [X] SNMPv3 user `JeremyOps` created with SHA auth and AES-128 privacy credentials
* [X] User and group settings verified via `show snmp user` and `show snmp group`
* [X] Engine counters and SNMP agent statistics validated
* [X] Configuration saved to NVRAM

---

## What I Practiced

* Standard ACL creation and implementation for device management plane security
* SNMPv2c read-only and read-write community assignment with ACL restriction
* System identification (`snmp-server contact` / `snmp-server location`) configuration
* SNMPv3 group structure using security model levels (`noauth`, `auth`, `priv`)
* SNMPv3 user creation incorporating SHA authentication and AES-128 encryption
* Management plane verification using `show snmp` command outputs

---

## Key Takeaways

* SNMPv2c uses cleartext community strings, making access control lists (ACLs) vital to prevent unauthorized network polling.
* SNMPv3 provides enhanced security by implementing Message Integrity, Authentication, and Encryption (Privacy).
* The three SNMPv3 security levels are:
* `noAuthNoPriv`: No authentication, no encryption.
* `authNoPriv`: Authentication via MD5/SHA, no encryption.
* `authPriv`: Authentication via MD5/SHA and encryption via DES/3DES/AES.
* Passwords defined for SNMPv3 users are case sensitive and must strictly match the management station profiles.
* Restricting management access via explicit ACLs on control-plane services protects network hardware against reconnaissance and modification attacks.
