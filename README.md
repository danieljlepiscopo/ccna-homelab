# CCNA Homelab
This is my homelab to help me prepare for the Cisco CCNA 200-301 certification. I'm using real Cisco IOS routers and switches to build practical skills in networking concepts like VLANs, STP, static routing, and more. The goal is to simulate a real-world enterprise environment and reinforce theory with hands-on experience.

## Devices Used
- Cisco 1921 Routers (x3)
- Cisco Catalyst 2960 Switches (x2)
- Cisco Catalyst 3560 Switches (x1)
- Windows 10 PC (Oracle VM with Win10 & Ubuntu VMs)

## Topology Overview
![CCNA Homelab Topology](topology/logical-diagram.png)

## Goals
1. Cable and Power On Devices               | ✅ Completed
2. Set Hostnames and Interface Descriptions | ✅ Completed
3. Device IPs and Default Gateways          | ✅ Completed
4. Enable SSH and Remote Access             | ✅ Completed
5. VLANs and Trunking                       | ✅ Completed
6. Implement STP/RSTP                       | ✅ Completed
7. Static Routing                           | ✅ Completed
8. Inter-VLAN Routing                       | ✅ Completed
9. DHCP and DNS                             | ✅ Completed
10. Dynamic Routing (RIP, EIGRP, and OSPF)  | ✅ Completed
11. IPv6 Routing                            | 🔲 Not Started
12. Basic ACLs and Port Security            | 🔲 Not Started
13. CDP/LLDP Verification                   | 🔲 Not Started
14. NAT/NAT Overload (PAT) Simulation       | 🔲 Not Started
15. Gateway Redundancy with HSRP            | 🔲 Not Started

## Key Subnets
| Device | Interface | IP Address      |
|--------|-----------|-----------------|
| PC     | NIC       | 192.168.1.10/24 |
| SW1    | VLAN 99   | 192.168.1.1/24  |
| SW2    | VLAN 99   | 192.168.1.21/24 |
| SW3    | VLAN 99   | 192.168.1.22/24 |
| R1     | Gi0/1     | 192.168.1.2/24  |
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
│   └── switches/
│       ├── SW1-running-config.txt
│       ├── SW2-running-config.txt
│       └── SW3-running-config.txt
└──labs/
    ├── lab1_cabling_setup.md
    ├── lab8_inter-vlan_routing.md
    ├── lab9_dhcp_and_dns.md
    └── lab10_dynamic_routing.md
 ```

## Skills Demonstrated
- Cisco IOS configuration and CLI navigation
- VLANs and 802.1Q trunking
- STP and RSTP implementation
- IP subnetting and static routing
- SSH and secure remote access setup
- Layer 2/3 troubleshooting

## License
- MIT License
