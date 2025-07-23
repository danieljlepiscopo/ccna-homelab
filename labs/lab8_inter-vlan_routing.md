# Lab 8: Inter-VLAN Routing

**Objective**: Enabled inter-VLAN routing on my Layer 3 switch (SW1) and added my Windows 10 and Linux Ubuntu VMs from my PC.


For this homelab, I enabled inter-VLAN routing on my SW1 and connected VMs on my PC. Let’s go through this whole process.

## Step 1: Buy Ethernet Adapters

To connect my Cisco CCNA homelab to my PC, I used USB-to-Ethernet adapters.

With these adapters, I was able to connect Ethernet cables from the switch directly into my PC via USB.

<center>
  <img width="400" height="500" alt="Ethernet Adapter" class="center" src="https://github.com/user-attachments/assets/93643b9a-270f-48c5-ba16-2adbb4ba0aae" />
</center>

---

## Step 2: Connect Ethernet Cables

I physically connected the switch to my PC as follows:

- `SW1 Fa0/5 → USB Ethernet Adapter 1`
- `SW1 Fa0/6 → USB Ethernet Adapter 2`

<center>
  <img width="400" height="500" alt="SW1 Connections" class="center" src="https://github.com/user-attachments/assets/fd36fa58-2b2d-4a83-bbd7-5a08b395e47e" />
</center>
<center>
  <img width="400" height="500" alt="PC USB Connections" class="center" src="https://github.com/user-attachments/assets/659de292-fddb-4b01-b0d5-5362df930de9" />
</center>

---

## Step 3: Assign VMs to the Interfaces

I used Oracle VirtualBox for my VMs (Windows 10 and Ubuntu). I adjusted their network settings:
<center>
  <img width="400" height="500" alt="Virtualbox Settings for Ubuntu VM" class="center" src="https://github.com/user-attachments/assets/7a8c4db6-06b7-4eb5-9f75-461a2dd66773" />
</center>
<center>
  <img width="400" height="500" alt="Virtualbox Settings for Windows 10 VM" class="center" src="https://github.com/user-attachments/assets/c24c32d2-e41e-4f49-8adf-a449d16b6e92" />
</center>

- **Attached To**: Bridged Adapter  
- **Promiscuous Mode**: Allow All  
- **Adapter 1**: Bound to the appropriate Ethernet interface (Adapter 1 for Ubuntu, Adapter 2 for Windows 10)

---

## Step 4: Layer 3 SVI Configuration on SW1

Cisco IOS Configuration:

```bash
conf t
!
vlan 10
 name Sales
vlan 20
 name HR
!
interface vlan 10
 ip address 192.168.10.1 255.255.255.0
 no shutdown
exit
!
interface vlan 20
 ip address 192.168.20.1 255.255.255.0
 no shutdown
!
interface FastEthernet0/5
 description Ubuntu VM (VLAN 10 - Sales)
 switchport access vlan 10
 switchport mode access
 spanning-tree portfast
 spanning-tree bpduguard enable
!
interface FastEthernet0/6
 description Win10 VM (VLAN 20 - HR)
 switchport access vlan 20
 switchport mode access
 spanning-tree portfast
 spanning-tree bpduguard enable
!
ip routing
end
wr
```

---

## Step 5: Configuring VM IPs

### Ubuntu VM

**IPv4 Configuration:**

<center>
  <img width="325" height="325" alt="Ubuntu IP Settings" class="center" src="https://github.com/user-attachments/assets/a5ad07ab-abe2-4366-b2ff-3b4a7b07c42f" />
</center>
<center>
  <img width="400" height="500" alt="Ubuntu Network Settings" class="center" src="https://github.com/user-attachments/assets/95455c63-0571-4daf-a794-0712273308db" />
</center>

- IP: `192.168.10.10`
- Netmask: `255.255.255.0`
- Gateway: `192.168.10.1`
- DNS: `8.8.8.8`

After saving, I was able to ping `192.168.10.1` (SW1’s VLAN 10 SVI).
<center>
  <img width="400" height="500" alt="Ubuntu ping gateway" class="center" src="https://github.com/user-attachments/assets/9e38a765-158d-4607-9aae-8859204b1d86" />
</center>

### Windows 10 VM

**IPv4 Configuration (set via Command Prompt):**
<center>
  <img width="400" height="500" alt="Windows Network Settings" class="center" src="https://github.com/user-attachments/assets/2938a126-70f1-423f-90ae-8a3d6775639b" />
</center>

- IP: `192.168.20.10`
- Netmask: `255.255.255.0`
- Gateway: `192.168.20.1`
- DNS: `8.8.8.8`

```cmd
netsh interface ipv4 show interfaces
netsh interface ipv4 set address name="Ethernet" static 192.168.20.10 255.255.255.0 192.168.20.1
```

This manually configured the static IP. After setting this, I was able to ping `192.168.20.1`.
<center>
  <img width="400" height="500" alt="Windows Network Settings" class="center" src="https://github.com/user-attachments/assets/d4774b3c-cc5b-4dd5-8d05-2dfbfaddfc5e" />
</center>

---

## Summary

This lab demonstrates Inter-VLAN routing on a Catalyst 3560 switch using SVIs and VMs as end hosts. The setup required USB Ethernet adapters, VirtualBox networking tweaks, and static IP assignments for both Ubuntu and Windows hosts.

---
