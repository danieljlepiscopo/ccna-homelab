# CCNA Homelab
Hands-on CCNA 200-301 homelab using real Cisco IOS routers and switches to master enterprise networking concepts through practical, documented labs.

## Devices Used
- Cisco 1921 Routers (x3)
- Cisco Catalyst 3560C (Layer 3)
- Cisco Catalyst 3650 24-POE (Layer 3)
- Cisco Catalyst 3560v2 (Layer 2)
- Cisco 2504 Wireless LAN Controller (WLC)
- Cisco 3602i Access Point (AP)
- Windows 10 PC (with Oracle VMs: Windows 10 & Ubuntu)

## Diagrams
### Logical Topology
![Logical Topology](topology/logical-diagram.png)

## Recent Lab Updates
As of November 2025, this homelab has completed 16 out of 18 planned labs.  
From the last lab, the homelab currently demonstrates:
- Understanding why we need QoS and the three main models: Best Effort, IntServ, and DiffServ.
- Going through Classification and Marking, Queueing, Policing/Shaping, and Congestion Avoidance.
- Demoed all QoS concepts with practical examples of service policies.

Next up: First Hop Redundancy Protocols with HSRP, VRRP, and GLBP.

Last updated: November 2025

## Goals
- [x] 1. Cable and Power On Devices
- [x] 2. Set Hostnames and Interface Descriptions
- [x] 3. Device IPs and Default Gateways
- [x] 4. Enable SSH and Remote Access
- [x] 5. VLANs and Trunking
- [x] 6. Implement STP/RSTP
- [x] 7. Static Routing (IPv4)
- [x] 8. Inter-VLAN Routing
- [x] 9. DHCP and DNS (IPv4)
- [x] 10. Dynamic Routing (RIP, EIGRP, OSPFv2)
- [x] 11. IPv6 Routing (OSPFv3)
- [X] 12. Wireless LAN (WLAN)
- [X] 13. IP ACLs and Security Services
- [X] 14. CDP/LLDP Device Discovery
- [X] 15. NAT/NAT Overload (PAT) Simulation
- [X] 16. QoS Fundamentals
- [ ] 17. FHRP with HSRP, VRRP, and GLBP
- [ ] 18. SNMP, NTP, and Syslog Monitoring

## Key Subnets
| Device | Interface | IP Address      |
|--------|-----------|-----------------|
| PC     | NIC       | 192.168.1.10/24 |
| SW1    | VLAN 99   | 192.168.1.1/24  |
| SW2    | VLAN 99   | 192.168.1.21/24 |
| SW3    | VLAN 99   | 192.168.1.22/24 |
| R1     | Gi0/1     | 192.168.1.2/30  |
| R1     | Gi0/0     | 192.168.3.1/30  |
| R1     | Lo0       | 192.168.2.1/32  |
| R2     | Gi0/1     | 192.168.3.2/30  |
| R2     | Gi0/0     | 192.168.5.1/30  |
| R2     | Lo0       | 192.168.4.1/32  |
| R3     | Gi0/1     | 192.168.5.2/30  |
| R3     | Lo0       | 192.168.6.1/32  |


## Routing Path
PC > SW1 > R1 > R2 > R3 (and back)

## Project Structure
 ```
ccna-homelab/
├── README.md
├── topology/
│   ├── ccna-homelab.jpg
│   ├── logical-topology.png
│   ├── physical-topology.png
│   └── topology-notes.md
├── configs/
│   ├── routers/
│   │   ├── R1-running-config.txt
│   │   ├── R2-running-config.txt
│   │   └── R3-running-config.txt
│   ├── switches/
│   │   ├── SW1-running-config.txt
│   │   ├── SW2-running-config.txt
│   │   └── SW3-running-config.txt
│   └── wireless/
│       ├── WLC-config.txt
│       └── AP1-config.txt
└──labs/
    ├── lab1_cabling_setup.md
    ├── lab8_inter-vlan_routing.md
    ├── lab9_dhcp_and_dns.md
    ├── lab10_dynamic_routing.md
    ├── lab11_ipv6_routing.md
    ├── lab12_wireless_lan.md
    ├── lab13_acls_and_security.md
    ├── lab14_cdp_lldp.md
    ├── lab15_nat_overload_pat.md
    ├── lab16_qos_fundamentals.md
    └── lab17_first_hop_redundancy.md

 ```

## Skills Demonstrated
- Cisco IOS configuration and CLI navigation
- VLANs and 802.1Q trunking
- STP and RSTP implementation
- IP subnetting and static routing
- OSPF, EIGRP, and RIP dynamic routing
- IPv6 addressing and OSPFv3
- SSH and secure remote access setup
- DHCP and DNS server configuration (Ubuntu BIND9)
- Layer 2/3 troubleshooting using `ping`, `traceroute`, `show`, and `debug`
- IPv6 Routing and Dual-Stack Networking
- Wireless LAN Controller and AP Configuration
- ACLs (Standard, Extended) and Security Services
- CDP and LLDP Configured
- Network Address Translation (Static, Dynamic, PAT)

## How to Use This Repo
1. Clone the repository:
   ```bash
   git clone https://github.com/yourusername/ccna-homelab.git
2. Explore the `/configs` directory for running configs.
3. Follow the `/labs` directory for step-by-step guides.

Feel free to fork, modify, and use this homelab structure for your own CCNA learning.

## License
- MIT License
