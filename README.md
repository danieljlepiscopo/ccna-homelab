# CCNA Homelab
This is my home lab to help me prepare for the Cisco CCNA 200-301. I'm using real Cisco routers and switches

## Devices Used
- Cisco 1921 Routers (x3)
- Cisco Catalyst 2960/3560 Switches (x3)
- Windows 10 PC

## Topology Overview
** PC (192.168.1.10)
   |
SW1 (VLAN 99: 192.168.1.1)
   |
R1 (Gi0/1: 192.168.1.2, Lo0: 192.168.2.1)
   |
R2 (Lo0: 192.168.4.1)
   |
R3 (Lo0: 192.168.6.1)**

## Goals
1. Cable and Power On Devices Correctly ✅ Completed
2. Set Hostnames and Interface Descriptions ✅ Completed
3. Configure Device IPs and Default Gateways ✅ Completed
4. Enable SSH and Remote Access ✅ Completed
5. Configure VLANs and Trunking ✅ Completed
6. Implement STP/RSTP ✅ Completed
7. Configure Static Routing ✅ Completed
8. Implement Inter-VLAN Routing 🔲 Not Started
9. Configure DHCP and NAT 🔲 Not Started
10. Set Up Basic ACLs and Port Security 🔲 Not Started
11. Set up routing protocols like RIP, OSPF, and or EIGRP between routers 🔲 Not Started

## Key Subnets
| Device | Interface | IP Address      |
|--------|-----------|-----------------|
| PC     | NIC       | 192.168.1.10/24 |
| SW1    | VLAN 99   | 192.168.1.1/24  |
| SW2    | VLAN 99   | 192.168.1.21/24 |
| SW2    | VLAN 99   | 192.168.1.22/24 |
| R1     | Gi0/1     | 192.168.1.2/24  |
| R1     | Gi0/0     | 192.168.3.1/30  |
| R1     | Lo0       | 192.168.2.1/24  |
| R2     | Gi0/1     | 192.168.3.2/30  |
| R2     | Gi0/0     | 192.168.5.1/30  |
| R2     | Lo0       | 192.168.4.1/24  |
| R3     | Gi0/1     | 192.168.5.2/30  |
| R3     | Lo0       | 192.168.6.1/24  |

## Routing Path
PC > SW1 > R1 > R2 > R3 (and back)

## Static Routes	
| Router | Static Routes                |
|--------|------------------------------|
| R1     | 192.168.4.0/24 → 192.168.3.2 |
| R1     | 192.168.5.0/30 → 192.168.3.2 |
| R1     | 192.168.6.0/24 → 192.168.3.2 |
| R2     | 192.168.1.0/24 → 192.168.3.1 |
| R2     | 192.168.2.0/24 → 192.168.3.1 |
| R2     | 192.168.6.0/24 → 192.168.5.2 |
| R3     | 192.168.1.0/24 → 192.168.5.1 |
| R3     | 192.168.2.0/24 → 192.168.5.1 |
| R3     | 192.168.3.0/30 → 192.168.5.1 |
| R3     | 192.168.4.0/24 → 192.168.5.1 |

## Project Structure
 ```
ccna-homelab/
├── README.md
├── topology/
│   ├── logical-topology.png
│   ├── physical-topology.png
│   └── topology-notes.md
├── configs/
│   ├── routers/
│   │   ├── R1-running-config.txt
│   │   ├── R2-running-config.txt
│   │   └── R3-running-config.txt
│   └── switches/
│   │   ├── SW1-running-config.txt
│   │   ├── SW2-running-config.txt
│   │   └── SW3-running-config.txt
├── labs/
│   └── 01-cabling-devices.md
└── notes/
    └── 01-cabling-device.md
 ```

## Skills Demonstrated
- Computer Networking
- Cisco IOS

## License
- MIT License
