# Topology Notes

This document explains the logical and physical topology of my CCNA Homelab setup, including interface assignments, VLANs, IP addressing, and cable connections.

---

## Logical Topology Overview

**Subnets and Devices**

| Subnet           | Devices/Interfaces                          |
|------------------|---------------------------------------------|
| 192.168.1.0/24   | PC, SW1 (VLAN 99), SW2, SW3, R1 Gi0/1       |
| 192.168.2.0/24   | R1 Lo0                                      |
| 192.168.3.0/30   | R1 Gi0/0 <--> R2 Gi0/1                       |
| 192.168.4.0/24   | R2 Lo0                                      |
| 192.168.5.0/30   | R2 Gi0/0 <--> R3 Gi0/1                       |
| 192.168.6.0/24   | R3 Lo0                                      |

**Loopback Interfaces**

- R1: Lo0 = 192.168.2.1/24
- R2: Lo0 = 192.168.4.1/24
- R3: Lo0 = 192.168.6.1/24

---

## Physical Topology Notes

**Device Roles**

- **SW1**: Core switch connected to PC and R1.
- **SW2/SW3**: Access switches (also used for STP/RSTP and EtherChannel).
- **R1-R3**: Routers used for static and dynamic routing.

**Cabling Summary**

| Connection           | Purpose                     |
|----------------------|-----------------------------|
| PC <--> SW1 Fa0/1    | End-user device connection  |
| SW1 Fa0/2 <--> SW2   | Trunking/VLANs/STP          |
| SW1 Fa0/3 <--> SW3   | Trunking/VLANs/STP          |
| R1 Gi0/1 <--> SW1    | Gateway to LAN              |
| R1 Gi0/0 <--> R2 Gi0/1 | Point-to-point router link |
| R2 Gi0/0 <--> R3 Gi0/1 | Point-to-point router link |

---

## VLAN Information

- **VLAN 99**: Management VLAN on all switches.
- All switch interfaces are assigned to VLAN 99 unless otherwise noted.

---

## Future Enhancements

- Add EtherChannel between SW2 and SW3
- Implement HSRP for gateway redundancy
- Label STP root bridge in diagram
