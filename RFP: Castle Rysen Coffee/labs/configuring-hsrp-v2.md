# HSRP High Availability and Gateway Failover

Castle Rysen’s fallout shelter routers are about to shoulder twice the load.

This lab introduces **HSRP (Hot Standby Router Protocol)** to provide redundant default gateways for the shelter VLANs. FS-R1 will operate as the preferred active router, while FS-R2 will remain ready to take over if FS-R1 becomes unavailable.

The lab covers HSRPv2 configuration, virtual gateway addressing, router priorities, preemption, and an actual failover test using a client connected to the shelter network.

## Training Objectives

* Confirm the shelter switch has VLANs 10, 20, 30, and 40 active on the router trunks.
* Reassign the fallout shelter user VLAN interfaces on FS-R1 so the first host address in each subnet can become virtual.
* Deploy HSRPv2 on FS-R1 with higher priority and preemption across all shelter VLANs.
* Join FS-R2 to the same HSRP groups and confirm its standby status.
* Simulate an FS-R1 failure and verify that FS-R2 takes over as the active gateway.
* Restore FS-R1 and confirm HSRP preemption returns the active role to the primary router.

---

# Network Diagram

<img width="1134" height="844" alt="image" src="https://github.com/user-attachments/assets/f73ba6bd-8b05-4ad9-aae2-4ebf0df85c3f" />

---

# HSRP Addressing Plan

| VLAN    | Subnet         | FS-R1       | HSRP Virtual IP | FS-R2       | HSRP Group |
| ------- | -------------- | ----------- | --------------- | ----------- | ---------: |
| VLAN 10 | 10.0.16.0/27   | 10.0.16.2   | 10.0.16.1       | 10.0.16.3   |         10 |
| VLAN 20 | 10.0.16.128/27 | 10.0.16.130 | 10.0.16.129     | 10.0.16.131 |         20 |
| VLAN 30 | 10.0.17.0/27   | 10.0.17.2   | 10.0.17.1       | 10.0.17.3   |         30 |
| VLAN 40 | 10.0.17.128/27 | 10.0.17.130 | 10.0.17.129     | 10.0.17.131 |         40 |

### HSRP Configuration

| Setting      | FS-R1   | FS-R2   |
| ------------ | ------- | ------- |
| HSRP Version | 2       | 2       |
| Priority     | 105     | Default |
| Preemption   | Enabled | Enabled |
| Role         | Active  | Standby |

The **HSRP virtual IP** becomes the default gateway used by clients. The physical router addresses remain separate so both routers can participate in the HSRP group.

---

# Task 0 — Prepare the Shelter VLAN Trunks

### Objective

Make sure the switch can carry every VLAN used by the HSRP groups.

In the lab, VLAN 10 already exists for the client port. VLANs 20, 30, and 40 were created.

1. From the `Shelter-SW` console, create VLANs 20, 30, and 40.
   
<img width="640" height="171" alt="image" src="https://github.com/user-attachments/assets/96de343f-1a4f-480b-9554-adc4c65a38a6" />

2. Confirm the trunk toward FS-R1 is carrying VLANs 10, 20, 30, and 40.
3. Confirm the trunk toward FS-R2 is carrying VLANs 10, 20, 30, and 40.

<img width="606" height="289" alt="image" src="https://github.com/user-attachments/assets/48f3d015-0205-4b39-b3d3-29e6d1b57b23" />

---

# Task 1 — Reclaim Router1 Address Space

### Objective

Free the first usable address in each subnet so it can become the HSRP virtual gateway.

FS-R1 will move to the **second usable address** in each subnet.

Interfaces were using the first usable IP address and E0/0 was administratively down, I enable it, and updated the addressing in all subinterfaces with 2nd usable IP:

<img width="645" height="124" alt="image" src="https://github.com/user-attachments/assets/16b1d3cf-3c89-4197-8a7a-23e94f2d3521" />

Second usable IP in each range.

<img width="637" height="118" alt="image" src="https://github.com/user-attachments/assets/96fd431a-1ebe-41ac-aa93-dc7a6598a18f" />

---

# Task 2 — Establish HSRPv2 on FS-R1

### Objective

Make FS-R1 the preferred active gateway for all shelter VLANs.

The first usable address of each subnet will become the **HSRP virtual IP address**.

### Steps

On each VLAN subinterface:

1. Enable HSRP version 2.
2. Configure the HSRP group using the VLAN ID.
3. Configure the virtual IP address.
4. Set the priority to `105`.
5. Enable preemption.

<img width="377" height="343" alt="image" src="https://github.com/user-attachments/assets/4e631ee9-8017-4c35-9b85-076fa3ef3258" />

### Verification

<img width="583" height="140" alt="image" src="https://github.com/user-attachments/assets/6b53eca7-6e14-4525-9916-54c1d28ccd63" />

---

# Task 3 — Join FS-R2 to the HSRP Groups

### Objective

Configure FS-R2 as the standby router for all four HSRP groups.

<img width="398" height="274" alt="image" src="https://github.com/user-attachments/assets/3a3fa08c-3830-4cb8-a063-1238e19ad12f" />

### Expected Result

FS-R2 report:

<img width="600" height="139" alt="image" src="https://github.com/user-attachments/assets/361209e2-0c94-4e46-835f-b58e39cac1f0" />

FS-R1 should appear as the active peer.

The expected relationship is:

```text
VLAN 10 → FS-R1 Active  → FS-R2 Standby
VLAN 20 → FS-R1 Active  → FS-R2 Standby
VLAN 30 → FS-R1 Active  → FS-R2 Standby
VLAN 40 → FS-R1 Active  → FS-R2 Standby
```
FS-R1 report:

<img width="596" height="143" alt="image" src="https://github.com/user-attachments/assets/474c2a71-b9d0-4edb-9062-9c0c4db130e9" />

---

# Task 4 — Prove the Failover

### Objective

Demonstrate that clients retain gateway connectivity when FS-R1 becomes unavailable.

Don't just configure HSRP — **prove that it actually works.**

### Step 1 — Simulate FS-R1 Failure

On FS-R1, I temporarily disable the `Ethernet0/0` interface.

<img width="831" height="191" alt="image" src="https://github.com/user-attachments/assets/dfcab02d-b077-466d-abec-ae6252908555" />

### Step 2 — Observe FS-R2

On FS-R2, monitor the HSRP status.

After the HSRP hold timer expires, FS-R2 should transition from Standby to Active:

<img width="701" height="91" alt="image" src="https://github.com/user-attachments/assets/fe486ded-6719-4b7b-a810-4d789316f0f2" />

### Step 3 — Test from FS-Client10

Connect to `FS-Client10`.

* The client is connected to VLAN 10.
* Its address belongs to `10.0.16.0/27`.
* Its default gateway is the HSRP virtual address:

<img width="483" height="188" alt="image" src="https://github.com/user-attachments/assets/21732f82-4bb8-42bb-932d-cd7b0af61309" />

### Step 4 — Restore FS-R1

Re-enable the `Ethernet0/0` interface on FS-R1.

Wait for HSRP to re-establish communication.

Because preemption is enabled and FS-R1 has the higher priority, FS-R1 should reclaim the Active role.

### Final Verification

Confirm the HSRP relationship returns to:

```text
FS-R1 → Active
FS-R2 → Standby
```

<img width="817" height="345" alt="image" src="https://github.com/user-attachments/assets/dd6594aa-d29f-4d7c-8ed7-e6adc041ba2a" />

---

# Verification Checklist

Use the following checklist as the completion record for the lab.

* [X] VLANs 10, 20, 30, and 40 exist on `Shelter-SW`.
* [X] FS-R1 trunk carries VLANs 10, 20, 30, and 40.
* [X] FS-R2 trunk carries VLANs 10, 20, 30, and 40.
* [X] FS-R1 uses the second usable address in each subnet.
* [X] FS-R2 uses the third usable address in each subnet.
* [X] HSRPv2 is enabled.
* [X] HSRP groups match the VLAN IDs.
* [X] FS-R1 has priority 105.
* [X] HSRP preemption is enabled.
* [X] FS-R1 reports Active.
* [X] FS-R2 reports Standby.
* [X] FS-R1 failure was simulated.
* [X] FS-R2 successfully transitioned to Active.
* [X] FS-Client10 maintained gateway connectivity during failover.
* [X] FS-R1 was restored.
* [X] FS-R1 reclaimed the Active role.
* [X] Final client connectivity was verified.

---

# Key Takeaways

### HSRP Provides a Virtual Default Gateway

Clients don't need to know which physical router is currently forwarding traffic. They simply use the HSRP virtual IP as their default gateway.

For example:

```text
Client
   |
   | Default Gateway: 10.0.16.1
   |
+--+------------------+
|                     |
FS-R1                 FS-R2
10.0.16.2             10.0.16.3
Active                 Standby
   \                     /
    \                   /
     +-- HSRP Group 10 +
            |
       Virtual IP
       10.0.16.1
```

If FS-R1 fails, FS-R2 takes ownership of the virtual gateway.

### Physical IP vs. Virtual IP

Each router needs its own physical IP address:

```text
FS-R1 = 10.0.16.2
FS-R2 = 10.0.16.3
```

The clients use neither of those as their gateway. Instead, they use:

```text
HSRP Virtual IP = 10.0.16.1
```

This separation is what allows the gateway to move between routers without requiring clients to change their configuration.

### Priority Determines the Preferred Router

FS-R1 uses priority `105`, while FS-R2 remains at the default priority.

Therefore:

```text
FS-R1 → Higher priority → Preferred Active router
FS-R2 → Lower priority  → Standby router
```

### Preemption Restores the Preferred Router

Without preemption, FS-R2 could remain Active after FS-R1 comes back online.

With preemption enabled, FS-R1 sees that it has the higher priority and takes the Active role again.

The overall process is therefore:

```text
Normal operation
FS-R1 = Active
FS-R2 = Standby

        ↓ FS-R1 fails

Failover
FS-R1 = Down
FS-R2 = Active

        ↓ FS-R1 returns

Preemption
FS-R1 = Active
FS-R2 = Standby
```

The important lesson is that **HSRP is not about making two routers share one IP address permanently**. It creates a virtual gateway identity that can be owned by whichever router is currently Active.
