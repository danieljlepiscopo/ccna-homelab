# First Hop Redundancy Protocols (FHRP) (HSRP, VRRP, and GLBP)

When it comes to networking, one crucial idea is how to prevent Single Points of Failure (SPoF) from causing an outage to happen, especially for hosts that rely on that connectivity. Using redundancy, such a redundant devices or links, can help limit the amount of SPoF in a network, if not eliminate them. However, we also need a way for our hosts to have redundancy to their gateways as well.

For this lab, we will gain an understanding of why we need **First Hop Redundancy Protocols (FHRP)**, and then go through each of the three main FHRPs: **Hot Standby Router Protocol (HSRP**), **Virtual Router Redundancy Protocol (VRRP)**, and **Gateway Load Balancing Protocol (GLBP)**.

In this lab, we will go over:

1. **What is FHRP?**
2. **FHRP Overview: HSRP, VRRP, and GLBP**
3. **Hot Standby Router Protocol (HSRP)**
4. **Virtual Router Redundancy Protocol (VRRP)**
5. **Gateway Load Balancing Protocol (GLBP)**
6. **Wrap-Up**

## What is FHRP?

Let’s take a step back and look at a network with SPoFs in it:

<center>
  <img width="400" height="400" alt="spof" class="center" src="https://github.com/user-attachments/assets/3b062a9a-7937-453a-a05f-99078b097604" />
</center>

- As you can see, if one of these links goes down, hosts in the subnet `10.1.1.0/24` won’t be able to connect to the devices/hosts in the next subnet.

Here is where FHRP fixes this issue, but let’s get a definition of what FHRP even is:

- **First Hop Redundancy Protocol (FHRP)**: is a collection of protocols that provides redundancy to gateway routers in a subnet.

Seems easy enough, let’s go ahead and add in redundant devices/links to this example topology next:

<center>
  <img width="400" height="400" alt="redundancy" class="center" src="https://github.com/user-attachments/assets/7653c74d-b74b-453f-9dd3-ce05a84b48d6" />
</center>

So, now that we have a redundant LAN, where hosts have to choose between two different routers to get to the WAN, how do we tell the hosts which router to choose? What if one router goes down? How do we tell the hosts to choose the active router that’s still up instead of the one that’s down?

With FHRP, we’ll use a virtual IP address and a virtual MAC address on both of the routers.

- **Virtual IP Address (VIP)**: The default gateway IP address for all of the devices in the subnet.
- **Virtual MAC Address (VMA)**: The virtual MAC address that the router will automatically create.

From here, once the router is considered the active router (gateway router), the other one becomes the standby router. The hosts will point to the active router, and if something happens to it, FHRP messages that the routers use to communicate with one another will tell the hosts to swap to the alternative gateway. Pretty cool, right? Next, let’s take a look at all of the available FHRP options that there currently are and how they compare with one another.

## **FHRP Overview: HSRP, VRRP, and GLBP**

For the overview of all three FHRP options:

- **Hot Standby Router Protocol (HSRP)**
- **Virtual Router Redundancy Protocol (VRRP)**
- **Gateway Load Balancing Protocol (GLBP)**

Cisco’s proprietary protocol, which is called Hot Standby Router Protocol (HSRP). Later, similar protocols were developed, for instance, Virtual Router Redundancy Protocol (VRRP), which is used for devices communicating with each other from different vendors. Lastly, we have Cisco’s more developed FHRP option, Gateway Load Balancing Protocol (GLBP), which is a little more specialized.

I will break down each of these different FHRP up next, and evening go through the configuration as well. Here’s a table of the three options:

| **Acronym** | **Full Name** | **Origin** | **Redundancy Approach** |
| --- | --- | --- | --- |
| HSRP | Hot Standby Router Protocol | Cisco | Active/Standby |
| VRRP | Virtual Router Redundancy Protocol | RFC 5798 | Master/Backup |
| GLBP | Gateway Load Balancing Protocol | Cisco | Load Balanced |

## Hot Standby Router Protocol (HSRP)

HSRP is one of the most common and commonly configured FHRP, since Cisco was the first to create such a concept in computer networking. 

### States

HSRP works with an active/standby model (sometimes referred to as *active/passive*):

- **HSRP Active**: The active router is the gateway for hosts.
- **HSRP Standby**: The router waiting to take over the active router.
- **HSRP Priority**: The router with the highest priority wins and becomes the active router.
    - If priorities tie, the router with the lowest IP address wins instead.

After the election of the HSRP priority router, the VIP and VMA are applied to it. 

### Messages

HSRP enables the failover of the next-hop device if the original active device fails. It does this by sending HSRP multicast messages to the routers:

- **Hello**: Sent between the active/standby devices every three seconds.
- **Resign**: Snet by the active router when it’s about to release its active role.
- **Coup**: Used when the standby router wants to become the active router.

These messages maintain HSRP, as well as inform the routers of their states and if they need to be updated.

### Interface Tracking/Versions

**Interface Tracking**

IOS allows for interface tracking, which tracks the interface state, IP routing table, and a bunch of other objects/indicators. When the tracked interface or objects fail, HSRP will reduce the router’s priority. As an example for this, let’s take a look at this image:

<center>
  <img width="400" height="600" alt="redundancy" class="center" src="https://github.com/user-attachments/assets/b1c1d8fc-bd56-40ee-8d26-71e04c6d2fad" />
</center>

- If R1’s WAN link went down, and R2’s WAN was still functional, traffic would be sent through R1 to R2, which isn’t exactly what we want. We want traffic to just go to R2 → WAN.

This really comes to the WAN link failing, so R1 needs to be able to notice that as well as lowering its own priority level so R2 becomes the active router. Once the WAN link of R1 becomes active again, R1 will notice this change and then revert the changes, becoming the active router again.

**Versions**

HSRP has two different versions: **HSRPv1** and **HSRPv2**. HSRPv2 allows for IPv6 support, and it has faster convergence using shorter Hello timers. Because of these updates, it’s better to use HSRPv2 in your network if you’re going to use it. 

Below, I’ll list the differences between these two versions of HSRP:

| Feaure | **HSRPv1** | **HSRPv2** |
| --- | --- | --- |
| IPv6 Support | No | Yes |
| Hello Timer Convergence | Seconds | Miliseconds |
| Range of Groups | 0 - 255 | 0 - 4095 |
| Virtual MAC Address | 0000.0C07.ACXX (XX = Group #) | 0000.0C9F.FXXX (XXX = Group#) |
| IPv4 Multicast Address | 224.0.0.4 | 224.0.0.102 |
- It’s essential to note that routers/switches must use the same HSRP version; otherwise, they won’t understand each other’s multicast messages and will ignore one another.
