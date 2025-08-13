
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

<img width="1791" height="888" alt="Image" src="https://github.com/user-attachments/assets/f9d57990-0a62-4f18-8249-3ee96ecc6dc8" />

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

Hub NAT & TRACK Outputs :
--------------------------------

<img width="772" height="885" alt="Image" src="https://github.com/user-attachments/assets/2b37ceda-275c-4121-8100-163b4ee5c382" />


HUB Ping and Trace Output via ISP1 :
----------------------------------------

<img width="867" height="293" alt="Image" src="https://github.com/user-attachments/assets/17a45e9f-3c32-45b0-8298-16c287cca9f5" />


Spk1,Spk2,Spk3 Ping, Traceroute & ip Route Logs via ISP1 :
---------------------------------------------------------------

<img width="1881" height="997" alt="Image" src="https://github.com/user-attachments/assets/16ab070d-2031-4ffc-9897-e068fdb389d6" />

DR NAT Ping & Trace Route Output via ISP1 :
------------------------------------------------

<img width="936" height="776" alt="Image" src="https://github.com/user-attachments/assets/be758f63-bce8-4877-a4cd-5ba153e6f775" />

### **Failover Condition (Hub → ISP1 or Hub → PE1 Down)**

Ping, Traceroute & ip Route Output via ISP2 :
---------------------------------------------------

<img width="836" height="500" alt="Image" src="https://github.com/user-attachments/assets/d15ef736-371b-42e6-8362-7acd4b1e5831" />

Ping & Trace Route Output via ISP2 :
----------------------------------------

<img width="1919" height="990" alt="Image" src="https://github.com/user-attachments/assets/3373a5d8-09f7-448d-b308-f1d72a961e9b" />

Track, Ping & Trace Route Output via ISP2 :
--------------------------------------------

<img width="1102" height="818" alt="Image" src="https://github.com/user-attachments/assets/a7eeadfb-7dcd-4efd-8c28-058704281137" />

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
