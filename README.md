# Project 7 — Full Enterprise Network Simulation (Capstone)

## Overview

This capstone project simulates a complete enterprise network connecting a Headquarters (HQ) site with a remote Branch office using Cisco Packet Tracer. It integrates core networking technologies from Projects 1–6, including VLANs, inter-VLAN routing, static routing, GRE tunneling, and access control lists.

## Network Topology

![Network Topology](project7_topology.svg)

## Technologies Used

| Technology | Purpose | Device |
|---|---|---|
| VLANs (10, 20, 30) | Department segmentation at HQ | Core-SW-HQ, Access-SW-HQ |
| 802.1Q Trunking | Multi-VLAN traffic between switches | Core-SW-HQ ↔ Access-SW-HQ |
| Inter-VLAN Routing (SVI) | Route traffic between VLANs | Core-SW-HQ (3560) |
| Static Routing | Route traffic between sites | Router-HQ, Router-Branch, Core-SW-HQ |
| GRE Tunnel (Tunnel10) | Encapsulate site-to-site traffic | Router-HQ ↔ Router-Branch |
| Extended ACLs | Block Branch from HR & Management | Router-Branch |

## IP Addressing Scheme

### Infrastructure

| Device | Interface | IP Address | Subnet Mask |
|---|---|---|---|
| Router-HQ | GigE0/0 | 192.168.1.1 | 255.255.255.0 |
| Router-HQ | GigE0/1 (WAN) | 10.0.0.1 | 255.255.255.252 |
| Router-HQ | Tunnel10 | 172.16.0.1 | 255.255.255.252 |
| Router-Branch | GigE0/0 | 192.168.40.1 | 255.255.255.0 |
| Router-Branch | GigE0/1 (WAN) | 10.0.0.2 | 255.255.255.252 |
| Router-Branch | Tunnel10 | 172.16.0.2 | 255.255.255.252 |
| Core-SW-HQ | GigE0/1 | 192.168.1.2 | 255.255.255.0 |
| Core-SW-HQ | VLAN 10 | 192.168.10.1 | 255.255.255.0 |
| Core-SW-HQ | VLAN 20 | 192.168.20.1 | 255.255.255.0 |
| Core-SW-HQ | VLAN 30 | 192.168.30.1 | 255.255.255.0 |

### End Hosts

| Host | IP Address | Gateway | VLAN/Subnet |
|---|---|---|---|
| PC-IT | 192.168.10.10 | 192.168.10.1 | VLAN 10 (IT) |
| PC-HR | 192.168.20.10 | 192.168.20.1 | VLAN 20 (HR) |
| PC-Mgmt | 192.168.30.20 | 192.168.30.1 | VLAN 30 (Mgmt) |
| PC-Admin | 192.168.30.10 | 192.168.30.1 | VLAN 30 (Mgmt) |
| PC-Sales | 192.168.40.10 | 192.168.40.1 | Branch LAN |
| PC-Support | 192.168.40.20 | 192.168.40.1 | Branch LAN |

## Key Configurations

### GRE Tunnel (Tunnel10)
```
! Router-HQ
interface Tunnel10
 ip address 172.16.0.1 255.255.255.252
 tunnel source GigabitEthernet0/1
 tunnel destination 10.0.0.2

! Router-Branch
interface Tunnel10
 ip address 172.16.0.2 255.255.255.252
 tunnel source GigabitEthernet0/1
 tunnel destination 10.0.0.1
```

### Static Routing
```
! Router-HQ
ip route 192.168.10.0 255.255.255.0 192.168.1.2
ip route 192.168.20.0 255.255.255.0 192.168.1.2
ip route 192.168.30.0 255.255.255.0 192.168.1.2
ip route 192.168.40.0 255.255.255.0 172.16.0.2

! Router-Branch
ip route 0.0.0.0 0.0.0.0 172.16.0.1

! Core-SW-HQ
ip route 0.0.0.0 0.0.0.0 192.168.1.1
```

### ACL Security Policy
```
! Router-Branch
access-list 101 deny   ip 192.168.40.0 0.0.0.255 192.168.20.0 0.0.0.255
access-list 101 deny   ip 192.168.40.0 0.0.0.255 192.168.30.0 0.0.0.255
access-list 101 permit ip any any

interface GigabitEthernet0/0
 ip access-group 101 in
```

## Verification Results

| Test | From | To | Result |
|---|---|---|---|
| Inter-VLAN Routing | PC-IT (VLAN 10) | PC-HR (VLAN 20) | ✅ Pass |
| Cross-Site Ping | PC-IT (HQ) | PC-Sales (Branch) | ✅ Pass |
| Traceroute via Tunnel | PC-IT | PC-Sales | ✅ 4 hops via 172.16.0.2 |
| ACL Allow | PC-Sales (Branch) | PC-IT (VLAN 10) | ✅ Permitted |
| ACL Block HR | PC-Sales (Branch) | PC-HR (VLAN 20) | ❌ Blocked |
| ACL Block Mgmt | PC-Sales (Branch) | PC-Admin (VLAN 30) | ❌ Blocked |

## Files Included

- `Project7-Enterprise-Network.pkt` — Cisco Packet Tracer file
- `Project7_Enterprise_Network_Simulation.pdf` — Full documentation with screenshots
- `topology.png` — Network topology screenshot
- `README.md` — This file

## Tools

- Cisco Packet Tracer
- Cisco 2911 Routers, 3560 L3 Switch, 2960 L2 Switches

## Author

**Daksh Patel** — CCNA Certified  
- GitHub: [github.com/Daksh2601](https://github.com/Daksh2601)  
- LinkedIn: [linkedin.com/in/pateldaksh](https://linkedin.com/in/pateldaksh)  
- Portfolio: [daksh2601.github.io/Portfolio](https://daksh2601.github.io/Portfolio)
