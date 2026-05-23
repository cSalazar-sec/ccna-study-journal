# Power over Ethernet (PoE)

## Overview

Power over Ethernet (PoE) allows a single Ethernet cable to carry both:

- Network data
- Electrical power

This eliminates the need for separate power adapters for many network devices.

---

## Core Concepts

### PSE — Power Sourcing Equipment

The device that provides power.

Examples:
- PoE network switches
- PoE injectors

### PD — Powered Device

The device receiving power.

Examples:
- IP Phones
- Wireless Access Points
- IP Cameras

---

## How PoE Works

With PoE enabled:

- The switch provides both connectivity and electrical power
- The end device receives both through the same Ethernet cable

This simplifies deployments and reduces cabling requirements.

---

# PoE Standards

| Standard | Common Name | Type | Maximum Power |
|---|---|---|---|
| 802.3af | PoE | Type 1 | 15.4W |
| 802.3at | PoE+ | Type 2 | 30W |
| 802.3bt | PoE++ | Type 3 | 60W |
| 802.3bt | PoE++ | Type 4 | 90W |

> Cisco also introduced UPOE before the industry standardized higher-power PoE under 802.3bt.

---

# Active PoE vs Passive PoE

> This distinction is extremely important.

## Active PoE

Active PoE is the intelligent and standardized implementation.

Before sending power:
1. The switch and device communicate
2. They negotiate power requirements
3. The correct amount of power is delivered

Protocols commonly involved:
- CDP (Cisco Discovery Protocol)
- LLDP (Link Layer Discovery Protocol)

### Characteristics
- Safer
- Smart negotiation
- Prevents accidental device damage
- Industry standard

---

## Passive PoE

Passive PoE does **not** negotiate power.

The device continuously sends electricity through the cable regardless of whether the connected device supports PoE.

### Characteristics
- No negotiation
- Constant power delivery
- Can damage unsupported devices
- Common in some legacy or proprietary equipment

---

## Simple Analogy

- **Active PoE** → polite negotiation
- **Passive PoE** → electricity first, questions later

---

# PoE Budget

A switch does not have unlimited power capacity.

When planning a PoE deployment, ask:

- Does the switch support PoE?
- What is the total PoE budget?
- How much power does each device require?

Example:
- A switch may support PoE
- But still lack enough total wattage for all connected devices

---

# Verification Commands

## Check PoE Usage

```bash
show power inline
```

This command helps verify:
- Total available PoE power
- Power currently in use
- Power consumption per interface
- Remaining PoE budget

---

# Real-World Devices Using PoE

- IP Phones
- Wireless Access Points
- Security Cameras
- VoIP Devices
- IoT Devices

---

# Key Takeaways

- PoE delivers power and data through the same Ethernet cable
- The switch acts as the PSE
- The end device acts as the PD
- Active PoE negotiates power safely
- Passive PoE continuously sends power
- Always verify the switch's total PoE budget
- `show power inline` is an important operational command
