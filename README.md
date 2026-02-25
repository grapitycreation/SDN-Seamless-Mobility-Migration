# 🚀 SDN Seamless Mobility & Migration

> Event-Driven Software-Defined Networking Controller ensuring uninterrupted connectivity during host mobility and dynamic topology changes.

📚 Course Project — Programmable Networking (SDN)  
🎓 University of Information Technology – VNU-HCM  
👨‍💻 Group 11  

---

## 📌 Project Overview

Modern networks such as enterprise WiFi, IoT infrastructures, and virtualized environments continuously experience topology changes:

- Devices roam between Access Points (**Mobility**)
- Hosts change switch/port (**Migration**)
- Links fail and recover dynamically

Traditional distributed networks rely on routing convergence, which often results in:

- Packet loss  
- Temporary blackholes  
- Session interruption  

This project implements a **custom event-driven SDN controller** that maintains **seamless connectivity** during topology changes.

Instead of waiting for routing convergence, the controller:

1. Detects topology events instantly  
2. Removes stale flow entries  
3. Relearns host location via ARP re-discovery  
4. Reinstalls optimal path dynamically  

➡ Result: Active sessions continue with minimal interruption.

---

## 🧠 Core Design Philosophy

> Seamless connectivity in SDN is NOT automatic.  
> It depends entirely on how the controller handles topology state transitions.

Mobility and Migration are treated under a unified logic:

> When topology state becomes invalid → replace it immediately.

Controller reaction pipeline:

1. Detect topology change (HostMove / PortDown / LinkDelete)
2. Remove outdated flows
3. Trigger ARP Re-discovery
4. Recompute and reinstall optimal path (on-demand)

---

## 🏗️ System Architecture
![Topology](https://github.com/grapitycreation/SDN-Seamless-Mobility-Migration/blob/main/Topology.jpg)

### A. Migration (Wired Network - Mininet)

- Hosts move between switches (port down/up)
- Continuous ICMP session during migration
- Controller reacts to link/port changes

### B. Mobility (Wireless Network - Mininet-WiFi)

- Stations roam between AP1, AP2, AP3
- ICMP session remains active during roaming
- Controller handles reassociation events

Both scenarios are controlled by a centralized **Ryu SDN Controller (OpenFlow 1.3)**.

---

## ⚙️ Technology Stack

| Component | Purpose |
|------------|------------|
| Ryu (OpenFlow 1.3) | Custom SDN controller logic |
| Open vSwitch (OVS) | Data plane forwarding |
| Mininet | Wired migration simulation |
| Mininet-WiFi | Wireless roaming simulation |
| Python 3 | Controller implementation |
| Ubuntu 22.04 | Development environment |

---

## 🧩 Controller Architecture

### 1️⃣ Event-Driven Topology Awareness

Controller listens to:

- `EventSwitchEnter`
- `EventLinkAdd / EventLinkDelete`
- `EventOFPPortStatus`
- `EventHostAdd`
- `Packet-In (ARP / ICMP)`

This provides real-time topology state.

---

### 2️⃣ Adaptive Routing Algorithm

Path computation:

```
Cost = α × HopCount + β × LinkLoad
```

Where:

- **HopCount** → number of switches in path  
- **LinkLoad** → estimated via PortStats  
- α = 1.0  
- β = 0.1  

Algorithm steps:

1. BFS finds all valid paths
2. Compute cost per path
3. Select lowest-cost path
4. Install bidirectional ARP + ICMP flows

✔ Avoid congestion  
✔ Fast recomputation  
✔ Suitable for dynamic topology  

---

### 3️⃣ ARP Re-Discovery (Core Feature)

Main issue in SDN mobility:

> Old ARP mapping points to wrong switch/port.

Instead of waiting for ARP timeout:

- Identify peers communicating with moved host
- Send targeted ARP requests
- Update IP–MAC–Port mapping immediately
- Reinstall correct flow

This prevents prolonged blackholes and enables near-seamless recovery.

---

## 🔬 Experimental Scenarios

### Scenario 1 — Wireless Roaming

- STA moves from AP1 → AP2
- ICMP session running continuously

Observed:
- Temporary delay spike
- Minimal packet loss
- Automatic path reinstallation
- Session continues

---

### Scenario 2 — Host Migration

Host moves between switches during communication.

Default Controller:
- Stale flow remains
- Blackhole occurs
- ~80% packet loss

Custom Controller:
- Flow removed instantly
- ARP re-discovery triggered
- New optimal path installed
- Communication restored rapidly

---

## 📊 Engineering Skills Demonstrated

### Networking

- Layer 2/3 forwarding behavior
- ARP consistency issues
- WiFi roaming mechanics
- Failure recovery handling

### SDN Engineering

- OpenFlow rule management
- Topology graph construction
- Event-driven controller logic
- Real-time state recomputation

### Algorithms

- BFS graph traversal
- Cost-based path optimization
- State invalidation & recomputation

### Systems Thinking

- Control-plane vs data-plane timing
- Convergence vs reactivity
- Distributed state consistency

---

## ▶️ How to Run

Start controller:

```bash
ryu-manager --observe-links controller/ryu.py
```

Run Mobility scenario:

```bash
sudo python3 mobility.py
```

Run Migration scenario:

```bash
sudo python3 migration.py
```

---

## ✨ Demo
Link: https://youtu.be/Bhw0FxUQ4RQ

## 🚀 Future Improvements

- Predictive handover (802.11r + ML prediction)
- ECMP / weighted Dijkstra routing
- TCP/UDP QoS measurement (jitter, throughput)
- Real hardware deployment (OpenWRT APs)

---

## 🎯 Why This Project Matters

This repository demonstrates the ability to:

- Build a functional SDN control system
- Design real-time reactive network logic
- Solve ARP/state inconsistency issues
- Implement cost-based dynamic routing
- Translate theory into working implementation

Applicable to:

- Data center networking
- SD-WAN
- Edge computing
- IoT infrastructure
- Cloud networking

---

## 👨‍💻 Authors

Group 11 – Programmable Networking (NT541)

- Đỗ Hoàng Anh  
- Võ Nguyễn Chiến  
- Trần Hữu Hiếu  
- Nguyễn Hoàng Duy Kha  
- Đào Duy Khang  

University of Information Technology – VNU-HCM
