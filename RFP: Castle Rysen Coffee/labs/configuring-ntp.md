# Configuring NTP

The shelter needs a consistent time source for network devices and logs. In this lab, `RTR-Training-01` will act as the local NTP master using Castle Rysen Standard Time. A loopback address will be used as the NTP source so the switch has a stable address to reach even if the physical topology changes.

## Objectives

- Configure `RTR-Training-01` with Castle Rysen Standard Time.
- Make `RTR-Training-01` the NTP master for the training network.
- Create a stable Loopback interface for NTP.
- Configure `SW-Training-01` to use the router as its NTP server.
- Verify connectivity and document the NTP association state.

---

## Task 0 — Establish the Castle Time Source

Configure `RTR-Training-01` as the shelter's authoritative clock.

### Steps

- Configure the `CRST` timezone with a UTC offset of `-7`.

<img width="433" height="19" alt="image" src="https://github.com/user-attachments/assets/b74aaf3e-aa7c-46d2-847a-834731279e17" />


- Set the router's clock

<img width="409" height="22" alt="image" src="https://github.com/user-attachments/assets/0027cd97-4f70-4d8f-9414-fc5b24a5fab0" />

- Configure the router as an NTP master.

<img width="697" height="181" alt="image" src="https://github.com/user-attachments/assets/858201d5-85eb-4d95-8f60-fe41a1c8839f" />

- NTP is now up and running

<img width="366" height="55" alt="image" src="https://github.com/user-attachments/assets/beb8eb2e-5b4f-46d7-bb5c-43227e70c7b8" />

---

## Task 1 — Publish a Stable NTP Interface

Create a loopback interface that can be used as a consistent NTP source.

### Steps

Create `Loopback100`.
Assign `10.22.100.1/32`.

<img width="690" height="130" alt="image" src="https://github.com/user-attachments/assets/a23ad892-8d5a-4eeb-b500-6442700b23da" />

- Configured the router to source NTP traffic from the loopback.

<img width="418" height="22" alt="image" src="https://github.com/user-attachments/assets/aeab1e9c-76ce-40b6-a9d2-96b51d93030a" />

### Verification

<img width="357" height="55" alt="image" src="https://github.com/user-attachments/assets/9f2d7f4f-06dc-4ffa-8915-e7cc4b999bab" />

The router should continue to report itself as the local stratum 8 NTP source.
---

## Task 2 — Configure the Access Switch

Configure `SW-Training-01` to use `RTR-Training-01` as its NTP server.

### Steps

```text
SW-Training-01> enable
SW-Training-01# configure terminal

SW-Training-01(config)# interface vlan 10
SW-Training-01(config-if)# ip address 10.0.18.11 255.255.255.0
SW-Training-01(config-if)# no shutdown
SW-Training-01(config-if)# exit

SW-Training-01(config)# ip default-gateway 10.0.18.1
SW-Training-01(config)# ip route 0.0.0.0 0.0.0.0 10.0.18.1
SW-Training-01(config)# clock timezone CRST -7
SW-Training-01(config)# ntp server 10.22.100.1
SW-Training-01(config)# end
```

### Verification

<img width="346" height="61" alt="image" src="https://github.com/user-attachments/assets/f68ecaca-7e36-4d74-8e92-352aabfeecb1" />

---

## Task 3 — Verify NTP 

The NTP association may remain at:

<img width="672" height="94" alt="image" src="https://github.com/user-attachments/assets/3c534155-bc85-40cf-b1cb-8106d8db1899" />

### Expected Results

In the live simulator, the switch show:

```text
Clock is unsynchronized, stratum 16, no reference clock
```

<img width="641" height="165" alt="image" src="https://github.com/user-attachments/assets/aaaecb6b-92ac-419b-ab29-7e34383c64ae" />

The important point is that **IP connectivity works, but the NTP association does not synchronize in this simulator**.

---

## Completion Check

- [X] `RTR-Training-01` uses `CRST -7`.
- [X] Router clock was set.
- [X] `RTR-Training-01` is configured as an NTP master.
- [X] Router reports stratum 8 with reference `127.127.1.1`.
- [X] `Loopback100` is configured as `10.22.100.1/32`.
- [X] NTP traffic is sourced from `Loopback100`.
- [X] `SW-Training-01` uses `10.0.18.11/24` on VLAN 10.
- [X] `SW-Training-01` can reach `10.22.100.1`.
- [X] `SW-Training-01` is configured to use `10.22.100.1` as its NTP server.
- [X] The `.INIT.` / `reach 0` NTP state is documented as a simulator limitation rather than treated as a configuration failure.
