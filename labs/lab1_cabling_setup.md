## Lab 1: Physical Cabling and Rack Setup

**Objective**: Power on, cable, and logically design a physical lab topology using Cisco routers/switches. This foundational step enables me to console into devices, assign IPs, and prepare for network configuration labs.

Less of a traditional “lab” and more of an actual necessity for me to power on, connect, and be able to configure my Cisco switches and routers.  This beginning step is what is needed for me to configure devices and create a topology to mimic real-world networks.

![lab1_rack_no_cables.jpg](attachment:90e7f168-14c4-4b96-ae2d-771a94ee8656:Cisco_switches_routers.jpg)

To start this, I needed to understand what type of topology I needed; most networks today use a three-tier approach:

1. **Core**
2. **Distribution**
3. **Access**

![image.png](attachment:a0bc699d-bb7c-47d2-a261-245721dafc06:image.png)

- **Core Layer** - Being the core of the network (the backbone), where L3 switches and routers typically help with the forwarding of IP traffic.
- **Distribution Layer** - This is where L3 devices, like L3 switches and routers, distribute data to end hosts.
- **Access Layer** - Where users get plugged into L2 switches, and it’s the closest layer that interacts more with end hosts.

For my topology, I decided to go with the “Collapsed Core”, where the two-tier approach is:

1. **Core**
2. **Access**

![image.png](attachment:b407ff1f-1457-45f3-952f-c4cf9ce9383c:image.png)

- I ended up choosing this topology approach because it reflects the three-tier approach (aside from the distribution layer), but it was practical enough with the 6 devices that I have.

Now that I have the idea of my topology, I need to expand that to my devices:

- `Cisco 1921 Routers (x3)`
- `Cisco Catalyst 2960 Switches (x2)`
- `Cisco Catalyst 3560 Switch (x1)`
- `My personal Windows 10 PC`

Since these Cisco devices all have Auto-MDIX, I won’t have to worry about having both straight-through and crossover cables. Here are the cables I have to work with my homelab setup:

- `Ethernet UTP Straight-Through Cables (x10)`
- `Rollover Cable (x1)`
- `6 FT PC-AC Outlet 15 Amp Power Cord 5-15P to C13 (x6)`
- `8-Socketed Power Strip (x1)`

My thought process was that I’ll use my L3 Cisco 3560 Switch as my “Core” device, and link my L2 Cisco 2960 switches off of it. After that, I’ll connect my 1921 routers off the core as well. Which ended up looking something like this:

### Cabling Map
```
SW1 Fa0/1 ←→ SW2 Fa0/1
SW1 Fa0/2 ←→ SW3 Fa0/1
SW1 Fa0/3 ←→ PC Ethernet NIC
SW1 Fa0/4 ←→ R1 Gi0/1
SW1 Fa0/5 ←→ R2 Gi0/1
SW1 Fa0/6 ←→ R3 Gi0/1
```

This is what I have for my “Core” tier:

- `L3 Cisco 3560 Switch (x1)`

And then my “Access” tier:

- `Cisco 1921 Routers (x3)`
- `Cisco Catalyst 2960 Switches (x2)`

Eventually, I’ll have to move my cabling around when it comes to routing my routers with static and dynamic routing, but this will be fine for the time being, just to be able to console in and start configuring everything. 

I got everything set up and powered on, and working!

<picture of my lab with cabling/powered on>
