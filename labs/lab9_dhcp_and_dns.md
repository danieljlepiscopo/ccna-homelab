
# Lab 9: DHCP and DNS

**Objective**: Configure DHCP and DNS services in a realistic two-layer home lab topology using Cisco IOS and two virtual machines (Linux Ubuntu and Windows 10).

This lab builds off the previous one, where Inter-VLAN routing was implemented. In this lab, DHCP is handled by the L3 switch (SW1), and DNS is hosted locally via `bind9` on the Ubuntu VM.

---

## Updated Topology

We redesigned the homelab to follow a more realistic enterprise two-layer architecture (core/distribution and access layer). Each VM was connected to a different access layer switch:

| Device         | Interface   | Connected To  | VLAN | Role            |
|----------------|-------------|----------------|------|------------------|
| SW2 Fa0/3      | Ubuntu VM   | Host           | 10   | Access           |
| SW2 Gi0/1      | SW1 Gi0/1   | Trunk          | 10   | Trunk to SW1     |
| SW3 Fa0/3      | Win10 VM    | Host           | 20   | Access           |
| SW3 Gi0/1      | SW1 Gi0/2   | Trunk          | 20   | Trunk to SW1     |

<center>
  <img width="400" height="500" alt="Fiber connections on switches" class="center" src="https://github.com/user-attachments/assets/75823065-ee40-4319-a790-1926f8d12bcd" />
</center>

### SW1 Trunk Configuration

```bash
conf t
!
interface Gi0/1
 description Fiber Downlink to SW2
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10
exit
!
interface Gi0/2
 description Fiber Downlink to SW3
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 20
exit
!
end
wr
```

### SW1 VLAN Interfaces

```bash
interface Vlan10
 description Sales (SVI to Ubuntu VM)
 ip address 192.168.10.1 255.255.255.0
!
interface Vlan20
 description HR (SVI to Win10 VM)
 ip address 192.168.20.1 255.255.255.0
```

---

## Part 1: DHCP Configuration

### Step 1: Choose the DHCP Server

SW1 was chosen as the DHCP server because:
- It's the routing point for VLANs 10 and 20
- It's in the distribution layer
- It receives broadcast traffic from access layer switches

### Step 2: SW1 DHCP Configuration

**VLAN 10 Pool:**
```bash
conf t
!
ip dhcp excluded-address 192.168.10.1 192.168.10.10
!
ip dhcp pool VLAN10-POOL
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 8.8.8.8
exit
!
end
wr
```

**VLAN 20 Pool:**
```bash
conf t
!
ip dhcp excluded-address 192.168.20.1 192.168.20.10
!
ip dhcp pool VLAN20-POOL
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.1
 dns-server 8.8.8.8
exit
!
end
wr
```

### Step 3: VM Configuration

Both VMs were configured to use DHCP via their OS settings.

- **Ubuntu VM**: Switched to Automatic DHCP via GUI. Received an IP from the VLAN 10 pool (192.168.10.12).
- **Windows 10 VM**: Configured to obtain IP and DNS automatically. Also received a valid IP (192.168.20.3).

### Step 4: Inter-VLAN Testing

- Windows VM could ping Ubuntu VM.
- Ubuntu VM couldn’t ping Windows VM at first due to Windows Firewall.
- Allowed ICMPv4 with PowerShell:

```bash
New-NetFirewallRule -DisplayName "Allow ICMPv4-In All" -Protocol ICMPv4 -IcmpType 8 -Direction Inbound -Action Allow -Profile Any
```

---

## Part 2: DNS Configuration

### Step 1: Install BIND9 on Ubuntu

```bash
sudo apt update
sudo apt install bind9 -y
```

### Step 2: Create Forward Zone

Edited `/etc/bind/named.conf.local`:

```bash
zone "internal.lab" {
    type master;
    file "/etc/bind/db.internal.lab";
};
```

Cloned template zone file and edited A records:

```bash
sudo cp /etc/bind/db.local /etc/bind/db.internal.lab
sudo nano /etc/bind/db.internal.lab
```

DNS Records:
```bash
@       IN      NS      ns1.internal.lab.
ns1     IN      A       192.168.10.12
ubuntu  IN      A       192.168.10.12
windows IN      A       192.168.20.3
```

### Step 3: Restart and Test DNS

```bash
sudo systemctl restart bind9
dig ubuntu.internal.lab @localhost
dig windows.internal.lab @localhost
```

### Step 4: Configure Ubuntu as Its Own DNS Resolver

```bash
sudo nano /etc/netplan/*.yaml
```

Added nameservers pointing to 127.0.0.1 under the interface section:

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: no
      addresses:
        - 192.168.10.12/24
      gateway4: 192.168.10.1
      nameservers:
        addresses:
          - 127.0.0.1
```

Applied changes:
```bash
sudo netplan apply
```

Tested DNS resolution using `ping windows.internal.lab`.

### Step 5: Configure Windows to Use Ubuntu as DNS

Changed IPv4 settings manually to use `192.168.10.12` as the preferred DNS server.

Verified with:
```bash
nslookup ubuntu.internal.lab
nslookup windows.internal.lab
```

---

## Conclusion

This lab demonstrated how to:

1. Configure DHCP on a Cisco L3 switch to serve two VLANs.
2. Set up a DNS server on a Linux VM using BIND9.
3. Resolve hostnames internally using manually configured A records.

While this lab used static DNS entries, future improvements could include Dynamic DNS (DDNS) to sync DHCP leases with DNS records automatically.
