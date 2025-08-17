# DHCP and DNS

In this lab, I want to understand how a DHCP (Dynamic Host Configuration Protocol) server assigns IP addresses dynamically to end hosts, as well as understand how a DNS (Domain Name System) server relates a domain to an IP address. To do this, I’m breaking up this lab into two parts: DCHP and DNS. Let’s get going on this!

## DHCP

Let’s start with DHCP first!

For DHCP to work, we’ll need to understand a couple of things:

1. **Determine our DHCP Server** 
2. **Add the Configuration to the DHCP Server for Each VLAN**
3. **Configure our VMs**
4. **Test and Troubleshoot**

### Determine our DHCP Server

A DHCP server is typically in the distribution layer of a network, so what makes the most logical/realistic sense is to have our SW1 be the designated DHCP server because:

1. It’s our Core/Distribution device
2. It’s already routing the client VLANs (10 and 20)
3. All of the VM broadcast traffic will inevitably hit SW1
4. We don’t need to configure IP helpers 

With all these reasons, SW1 is our DHCP server in the home lab. Let’s move on to configuring it!

### Configure the DHCP server (SW1) for each VLAN

To configure SW1 as our DHCP server, we’ll need to set up a DHCP pool for VLAN 10 and VLAN 20. 

**VLAN 10**

For configuring VLAN 10 on SW1, we’ll use the `192.168.10.0/24` pool, where `192.168.10.1` will be the default gateway for VLAN 10. Here is the configuration:

```python

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

So, here is a breakdown of the commands/logic:

1. `excluded-address`: I give SW1 a pool list of 9 host IP addresses, and we’ll reserve the  `.1` on SW1 as the default
2. `VLAN10-POOL`: Reference pool name for VLAN 10
3. `default-router`: The SVI’s IP that hosts will use as their gateway
4. `dns-server`: Google’s 8.8.8.8 DNS server, just something safe and can be changed later

We’ll also ping both the Ubuntu VM and back to SW1 to make sure connectivity is there. Next, we’ll configure the Windows 10 VM on VLAN 20.

**VLAN 20**

For VLAN 20, we’ll do a similar configuration, but this time using the `192.168.20.0/24` pool, where `192.168.20.1` will be the default gateway for VLAN 20. Here is the configuration:

```python
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

So, here is a breakdown of the commands/logic:

1. `excluded-address`: I give SW1 a pool list of 9 host IP addresses, and we’ll reserve the  `.1` on SW1 as the default
2. `VLAN20-POOL`: Reference pool name for VLAN 20
3. `default-router`: The SVI’s IP that hosts will use as their gateway
4. `dns-server`: Google’s 8.8.8.8 DNS server, just something safe and can be changed later

We’ll also ping both the Windows VM and back to SW1 to make sure connectivity is there. Next, we’ll need to go into our VMs and configure our settings for DHCP.

### Configure our VMs

Up next, we’ll need to use DHCP to automatically assign IPs instead of us manually configuring them. I’ll break down each VM and how I configured it:

**Ubuntu VM**

This was a very easy configuration setup. Here are the steps I followed:

1. Open Settings 
    - Click on the Ubuntu Logo at the bottom left of the screen and then go to: `Show Applications` → `Settings` → `Network`
2. Select the Wired Ethernet connection
    - You should see the wired connection at the top
    - Click on the gear icon to the right of it
3. Go to IPv4 tab
    - Confirm that the IPv4 method is changed from Manual to Automatic DHCP
4. Save and Apply
    - Click on Apply and Save (if it wasn’t already changed)
5. Disable and Re-enable the interface
    - Toggle the interface off and back on or reboot the VM
6. Verify the IP Address
    - Open up a terminal and type the command:
    
    ```bash
    ip a
    ```
    
    - We should see a host IP within the range of the DHCP pool we issued: `.1 - .254`
7. Ping the Default Gateway
    - Test the connectivity between the VM and SW1 by running the command:
    
    ```bash
    ping 192.168.10.1
    ```
<center>
  <img width="400" height="500" alt="Fiber connections on switches" class="center" src="https://github.com/user-attachments/assets/9acde97c-67d6-4fb5-8741-26569408d926" />
</center>
<center>
  <img width="400" height="500" alt="Fiber connections on switches" class="center" src="https://github.com/user-attachments/assets/0f841178-6fa8-4ccc-9b4a-f9f0cbb80ead" />
</center>

Now that we have all of the configuration correct and I’m able to ping the default gateway and back, let’s move on to the configuration of the Windows VM.

**Windows10 VM**

This was a very easy configuration setup as well. Here are the steps I followed:

1. Go to Network Settings
2. Make sure the network adapter is pointing to the USB-to-Ethernet interface of Fa0/3 on SW3
3. Go to:
    - Control Panel > Network and Internet > Network and Sharing Center
    - Click on Change adapter settings
4. Right-click on your active Ethernet adapter > Properties
5. Scroll down and select **Internet Protocol Version 4 (TCP/IPv4)** > click **Properties**
    - Select **Obtain an IP address automatically**
    - Select **Obtain DNS server address automatically**
    - Click OK and close everything
6. Verify IP address and ping the default gateway:

```bash
ipconfig /all
ping 192.168.20.1
```

<center>
  <img width="400" height="500" alt="Fiber connections on switches" class="center" src="https://github.com/user-attachments/assets/eb3e1ef2-aa44-4d6f-b8b6-6bd3350858e7" />
</center>
<center>
  <img width="400" height="500" alt="Fiber connections on switches" class="center" src="https://github.com/user-attachments/assets/9502d5f4-6a30-4ec5-bc91-0107bd81ea60" />
</center>

After everything looks good on the Windows 10 VM, and you’re able to ping the default gateway, the last big thing for DHCP is making sure that Inter-VLAN routing is working by being able to ping each other’s default gateways and one another’s IP addresses.

- I was easily able to ping both the default gateway and the Ubuntu VM from the Windows VM.
- However, the Ubuntu VM was able to ping the default gateway, but not the Windows 10 VM.

So, Inter-Vlan was working, since I was able to ping the default gateway, I must be running into Windows 10 Defender Firewall, where the ICMP IPv4 Echo Request must be dropping. To test this, I disabled Defender, and I was able to ping from the Ubuntu VM:

<center>
  <img width="400" height="500" alt="Fiber connections on switches" class="center" src="https://github.com/user-attachments/assets/7ed75b11-2c45-4a0a-a0f5-13b3bce850b9" />
</center>

To fix this, it seems that Windows 10 VM GUI isn’t the greatest, so I found a bash command to turn up all of the ICMPv4 profiles on the firewall:

```bash
New-NetFirewallRule -DisplayName "Allow ICMPv4-In All" -Protocol ICMPv4 -IcmpType 8 -Direction Inbound -Action Allow -Profile Any

```

- I put this into the Administrative version of PowerShell, and it took the command.

<center>
  <img width="400" height="500" alt="Fiber connections on switches" class="center" src="https://github.com/user-attachments/assets/df6473c6-9849-44b6-81ca-b5716e3cd347" />
</center>

After this configuration was in, I was successfully able to ping the Windows 10 VM from the Ubuntu VM!

<center>
  <img width="400" height="500" alt="Fiber connections on switches" class="center" src="https://github.com/user-attachments/assets/91b616bb-9d98-4f36-9dac-1d4449a3e1f6" />
</center>

## DNS

Now that we have DHCP configured and working, let’s move on to DNS!

For this second part of the lab, we will simulate a basic DNS server in the homelab, without needing internet access. We can manually map hostnames to IP addresses using a local DNS server running on the Ubuntu VM. To do that, we’ll have to follow these steps:

1. **Install and Configure `bind9` DNS Server on the Ubuntu VM**
2. **Create a Forward Zone (e.g., `internal.lab`)**
3. **Restart and Test our DNS Server**
4. **Set up the Ubuntu VM to Use Itself as a DNS Resolver**
5. **Set Windows VM to Use Ubuntu as DNS Server**

Once all this is configured, we can test our DNS server on the Windows VM to see if everything is configured correctly.

### Install and Configure `bind9` DNS Server on the Ubuntu VM

The reason why we’re using BIND9 (Berkeley Internet Name Domain version 9), that it’s a widely used open-source software suite that provides a ton of DNS services. It’s perfect for my homelab, and it’s pretty easy to get set up and going. 

First, we’ll need to download BIND9 with these commands on the Ubuntu VM:

```bash
sudo apt update
sudo apt install bind9 -y
```

- After BIND9 is installed, we’ll then have to configure the DNS zone files next.
- Make sure you have an internet connection for this update/download.

### Create a Forward Zone (e.g., `internal.lab`)

These DNS zone files are text files that map IP addresses to and domain names, BIND9’s way of being able to specify all the information you want to within the DNS “zone” you create. 

To put this more succinctly, we’re going to set up our own private DNS server inside the homelab, similar to how GoDayy and Google Domains control DNS records for public websites.

- Where `internal.lab` will be our private domain
- The Ubuntu VM will act like an authoritative name server (like GoDaddy or Google Domains)

For this step, let’s call the homelab DNS zone (or text-file) the `db.internal.lab`. 

First, we’ll edit the main `named.conf.local` file using nano (almost like the main method in Java):

```bash
sudo nano /etc/bind/named.conf.local
```

- If you’ve never used nano before, it’s basically like a text editor, but it’s what will be configured on the said file.

Once we’re in, we’ll add this at the bottom to create our internal lab db:

```bash
zone "internal.lab" {
		type master;
		file "/etc/bind/db.internal.lab";
};
```

- This nano config is telling BIND9 that it is the master for the zone called `internal.lab`, and the DNS records for this zone are stored in `db.internal.lab`.

After that, let’s create our DNS zone file:

```bash
sudo cp /etc/bind/db.local /etc/bind/db.internal.lab
sudo nano /etc/bind/db.internal.lab
```

Next, since we now have our DNS zone created, let’s make the contents within the zone look just like this:

```bash
$TTL    604800
@       IN      SOA     ns1.internal.lab. admin.internal.lab. (
2         ; Serial
604800         ; Refresh
86400         ; Retry
2419200         ; Expire
604800 )       ; Negative Cache TTL

; Nameservers
@       IN      NS      ns1.internal.lab.

; A Records
ns1     IN      A       192.168.10.12
ubuntu  IN      A       192.168.10.12
windows IN      A       192.168.20.3
```

- This file is our DNS database, where we’ve mapped hostnames to IP addresses:
    - ns1.internal.lab = Ubuntu VM (the authoritative name server)
    - ubuntu.internal.lab = Ubuntu VM
    - windows.internal.lab = Windows VM
- We’re telling the Ubuntu VM that it’s in charge of the house, and these are the people that live in it.

### Restart and Test our DNS Server

Next up, let’s go ahead and restart BLIND9 and then test from the Ubuntu VM. We’re going to make sure that the Ubuntu VM knows that it’s indeed the self-hosted authoritative DNS server.

You can restart the DNS services:

```bash
sudo systemctl restart bind9
```

- The reason why we need to restart the services is that it ensures that all of the zone file changes are applied.

After that, you can test both of the VMs from the Ubuntu VM:

```bash
dig ubuntu.internal.lab @localhost
dig windows.internal.lab @localhost
```

- The dig command tells your system to check the local DNS server (127.0.0.1) what the IP is of the domain you’re specifying.

You should see the resolved hostnames to IPs in the `internal.lab` domain. We’re all done with testing! Next, let’s make sure the Ubuntu VM knows that it’s the DNS resolver now.

### Set the Ubuntu VM to Use Itself as a DNS Resolver

For this step, we need to tell the Ubuntu VM that it needs to resolve hostnames by itself (127.0.0.1), not anywhere else, like Google (8.8.8.8), for instance. To start this, we’ll need to edit the YAML file in the `/etc/netpath*.yaml` path:

Edit the Netplan config:

```bash
sudo nano /etc/netplan/*.yaml
```

Make sure that the YAML file looks just like this:

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

Apply the changes:

```bash
sudo netplan apply
```

After that, try and ping the Windows 10 VM:

```bash
ping windows.internal.lab
```

- Notice that we’re pinging with the local hostname instead of its IP address

We did all of this to make sure that the DNS queries we’re pointing inward, resolving any internal-only domains that we defined earlier in the custom DNS zone file.

### Set Windows VM to Use Ubuntu as DNS Server

For this last step, we will go to the Windows VM and see if it can see the Ubuntu hostname as well as its own:

First, we will go to the network settings of the VM:

<center>
  <img width="400" height="500" alt="Fiber connections on switches" class="center" src="https://github.com/user-attachments/assets/a58c97ff-b660-4b5a-a9aa-d36aa425e6e9" />
</center>

Next, we will manually assign the DNS server to the Ubuntu VM: `192.168.10.12`

<center>
  <img width="400" height="500" alt="Fiber connections on switches" class="center" src="https://github.com/user-attachments/assets/bf4b9a03-8cf6-410c-8b43-d03a73eeb9d3" />
</center>

Last, we will open up Command Prompt and see if we can `nslookup` the hostnames:

<center>
  <img width="400" height="500" alt="Fiber connections on switches" class="center" src="https://github.com/user-attachments/assets/f9a1d858-9e62-4f35-9f40-aca29833f4c8" />
</center>

- Seeing that the Server: UnKnown is very normal. Windows doesn’t recognize the hostname of the Ubuntu VM, mostly because we didn’t configure a reverse DNS PTR record for this setup. The important part is that the hostnames point to the correct IP address.
- Now that both the Windows VM and the Ubuntu VM know that the Ubuntu VM is the DNS server, we can close out this lab!

## Conclusion

This lab was a lot to get set up correctly. I ran into a ton of issues with the Ubuntu VM not being able to download the BIND9 application, so I ran all of these commands to make sure my VM was running on the current software:

```bash
sudo apt update
sudo apt upgrade
sudo apt full-upgrade
sudo apt autoremove
```

- After that, I was able to install BIND9, and the `named.conf.local` file was showing up correctly.

Ultimately, it was fun understanding DHCP and DNS and how they both work together to allow IP addresses and domains to be able to communicate with one another effectively. One point I do want to end on is that this isn’t the end for DNS and DHCP; we manually configured the A records, so if the IP address of, let's say, the Windows VM changes, then everything fails. 

To counter this, Dynamic DNS (DDNS) allows DHCP and DNS servers to both integrate and communicate with one another, so if a host device receives a new IP address that’s leased, it will update the records automatically. Maybe that’s something I’ll try in the future. For right now, this was perfect to:

1. Get my homelab set up correctly with a more realistic architecture
2. Configure DHCP on my L3 switch and apply it to my VMs (Ubuntu and Windows 10)
3. Set up my Ubuntu VM as the DNS server to resolve domains

In the next lab, I’ll start configuring dynamic routing (RIP, EIGRP, and OSPF)!
