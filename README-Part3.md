# Multi-PE MPLS VPN Lab – Part 3: Internet Breakout, NAT Failover & Dual ISP Resilience

## 📌 Overview
This is **Part 3** of my Multi-PE MPLS VPN lab series. In this stage, I extend the Hub–Spoke + DR topology from Parts 1 & 2 to provide:

- **Internet breakout** at the Hub site via ISP1.
- **Automatic failover** to DR site via ISP2 if Hub → ISP1 or Hub → PE1 link fails.
- **NAT failover** with back-to-back NAT between Hub and DR.

The design ensures that all spokes continue to access the internet through the DR site in the event of a Hub failure — **no manual intervention required**.

---

## 🛠 Technologies & Protocols Used
- **MPLS L3VPN** with **MP-BGP VPNv4** for PE-to-PE communication.
- **VRF Route-Target (RT) manipulation** for Hub-to-Spoke and DR-to-Spoke control.
- **OSPF** as PE-CE IGP with Sham-Link from Part 2 for DR resilience.
- **IP SLA + Track** for default route monitoring and dynamic failover.
- **NAT** at both Hub and DR for internet access and redundancy.

---

## 🔹 Lab Design

**Primary Path (Normal Operation)**
1. Hub advertises a default route (`0.0.0.0/0`) learned from ISP1 into OSPF.
2. PE1 redistributes this into BGP VPNv4 so spokes receive the default route from Hub.
3. Hub NATs spoke traffic out towards ISP1.

**Failover Path**
1. IP SLA on Hub monitors ISP1 gateway reachability (`11.11.11.1`).
2. If IP SLA fails, tracked default route to ISP1 is removed, and DR’s default route becomes active.
3. Traffic flows from spokes → DR → ISP2.
4. DR performs NAT for all incoming traffic from Hub/Spokes.

**Back-to-Back NAT**
- Hub NATs to DR when ISP1 is down.
- DR then NATs to ISP2’s public IP.
- Ensures correct address translation and return path symmetry.

---

## ⚙️ Key Configurations

### **Hub (R6) – Primary ISP NAT & IP SLA**
```bash
ip sla 1
 icmp-echo 11.11.11.1 source-interface e0/2
 frequency 5
ip sla schedule 1 life forever start-time now

track 10 ip sla 1 reachability

ip route 0.0.0.0 0.0.0.0 e0/2 track 10
ip route 0.0.0.0 0.0.0.0 e0/1 200   ! Backup via DR

ip nat inside source list NAT_ACL interface e0/2 overload
```

### **DR (R10) – Backup ISP NAT**
```bash
ip nat inside source list NAT_ACL interface e0/2 overload
```

---

## 🧪 Testing & Verification

### **Normal Condition (Hub → ISP1 Active)**
- Spokes’ default route → Hub → ISP1.
- NAT translations visible on Hub.
- Traceroute from spokes shows first hop via Hub.

### **Failover Condition (Hub → ISP1 or Hub → PE1 Down)**
- IP SLA detects failure → Track removes ISP1 default route.
- DR default route becomes active in BGP.
- Spokes now route traffic via DR → ISP2.
- NAT translations visible on DR.

---

## 📂 Repository Contents
```
README.md                     # This file
Multi-PE-Part3.unl             # EVE-NG topology file
configs/all-router-configs.txt # Consolidated configs
outputs/
   hub-via-isp1.png
   spokes-via-isp1.png
   spokes-via-isp2.png
   dr-nat-output.png
```

---

## 🚀 How to Reproduce
1. Deploy `.unl` file in EVE-NG.
2. Apply configs from `configs/` to each router.
3. Verify normal operation with ISP1 up.
4. Shutdown Hub–ISP1 or Hub–PE1 link.
5. Observe failover to DR → ISP2.

---

## 📌 Notes
- Part 3 builds directly on the configs from Parts 1 and 2.
- In production, consider adding BFD for faster SLA detection.
- Ensure proper NAT ACLs and route filtering between Hub and DR to prevent loops.

---
