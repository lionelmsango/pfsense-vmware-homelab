# pfsense-vmware-homelab
Enterprise-grade pfSense firewall lab with Windows 11 and Ubuntu Desktop - complete network security environment


# pfSense Firewall Home Lab - VMware Setup

My first pfSense firewall project - building a complete enterprise-grade network security lab from scratch using VMware Workstation.

---

## What I Built

I set up a complete home lab environment with pfSense firewall to learn network security fundamentals. This project taught me how firewalls work, how to configure network interfaces, create security policies, and manage a multi-OS network environment.

**Key Components:**
- VMware Workstation virtualization platform
- pfSense 2.7 firewall with dual interfaces
- Windows 11 Pro workstation
- Ubuntu Desktop 24.04 LTS workstation
- Complete firewall rule configuration
- DHCP and NAT services
- Cross-platform network testing


---

## Network Topology

Here's the network I built:



**Logical Layout:**
```
        Internet
           │
      VMware NAT
           │
    ┌──────────────┐
    │   pfSense    │
    │   Firewall   │
    └──────────────┘
    WAN │ LAN
        │
   Internal Network
    (192.168.10.0/24)
        │
    ╭───┴───╮
    │       │
Windows 11  Ubuntu
  (Pro)    Desktop
192.168.   192.168.
  10.100    10.101
```

---

## Network Design

### IP Addressing Scheme

| Device | Interface | IP Address | Subnet Mask | Gateway | Purpose |
|--------|-----------|------------|-------------|---------|---------|
| pfSense | WAN (em0) | DHCP from NAT | 255.255.255.0 | NAT gateway | Internet connection |
| pfSense | LAN (em1) | 192.168.10.1 | 255.255.255.0 | - | Internal gateway |
| Windows 11 | Ethernet0 | 192.168.10.100 | 255.255.255.0 | 192.168.10.1 | Test workstation |
| Ubuntu Desktop | ens33 | 192.168.10.101 | 255.255.255.0 | 192.168.10.1 | Test workstation |

**Network Details:**
- Internal Network: 192.168.10.0/24
- DHCP Range: 192.168.10.100-200
- Gateway: 192.168.10.1 (pfSense)
- DNS Servers: 8.8.8.8, 8.8.8.8 (Google)
- Total Available IPs: 254


---

## Installation Journey

### Phase 1: Setting Up VMware Workstation

I started by installing VMware Workstation Player on my Windows host machine and followed the installation wizard carefully.

**VMware Workstation Player Details:**
- Version: 17.5
- Type: Free for personal use
- Host OS: Windows 11
- Features: Full virtualization, multiple VMs, virtual networking
- 

---

### Phase 2: Getting pfSense

I downloaded pfSense Community Edition from the official website. The file came as a compressed ISO (.gz format), so I had to extract it using 7-Zip before I could use it.

---

### Phase 3: Creating the pfSense Virtual Machine

This was the trickiest part, setting up the VM correctly with two network adapters. pfSense needs two interfaces: one for internet (WAN) and one for the internal network (LAN). The most critical part was adding a second network adapter manually. By default, VMware only adds one adapter, but pfSense requires two. For the WAN interface, I used NAT networking, which allows pfSense to get internet access from my host machine. For the LAN interface, I created a custom virtual network (VMnet2). This isolates the internal network from direct internet access. That way, all traffic must go through pfSense.

![LAN Network Configuration](https://github.com/lionelmsango/pfsense-vmware-homelab/blob/5bf43e56bab2482bb5b500f2eae96ee48894b54e/Screenshots/03-vm-creation-04-lan-network-vmnet.png.jpg)

---

### Phase 4: Installing pfSense

I powered on the pfSense VM and watched it boot up for the first time. The installation was straightforward. 

![Installing pfSense](https://github.com/lionelmsango/pfsense-vmware-homelab/blob/5bf43e56bab2482bb5b500f2eae96ee48894b54e/Screenshots/04-installation-01-pfsense-booting.png.jpg)
![Installing pfSense](https://github.com/lionelmsango/pfsense-vmware-homelab/blob/5bf43e56bab2482bb5b500f2eae96ee48894b54e/Screenshots/04-installation-02-install-menu.png.jpg)
![Installing pfSense](https://github.com/lionelmsango/pfsense-vmware-homelab/blob/5bf43e56bab2482bb5b500f2eae96ee48894b54e/Screenshots/04-installation-04-install-complete.png.jpg)

After the first successful boot, pfSense presented a console menu. This is where I could configure the basic network settings before accessing the web interface.

---


### Phase 5: Initial pfSense Configuration

After installation, pfSense automatically detected both network interfaces. I needed to assign them correctly and configure the basic network settings.

**Interface Assignment:**
- **em0 → WAN** (gets internet from VMware NAT)
- **em1 → LAN** (internal network for my test machines)

Next, I configured the LAN interface with a static IP address.

**LAN Configuration Details:**
- IP Address: 192.168.10.1/24
- DHCP Server: Enabled
- DHCP Range: 192.168.10.100 - 192.168.10.200
- DNS: Forwarded to 8.8.8.8, 8.8.4.4

The console confirmed I could now access the web interface at https://192.168.10.1 

![LAN Configuration](https://github.com/lionelmsango/pfsense-vmware-homelab/blob/5bf43e56bab2482bb5b500f2eae96ee48894b54e/Screenshots/05-configuration-02-lan-configured.png.jpg)

---

### Phase 6: Creating Realistic Test Machines

I needed computers on the internal network to actually test the firewall. 

---

#### Setting Up Windows 11

I downloaded Windows 11 from Microsoft's official website and created a VM with realistic business specs. The key here was making sure the network adapter was set to VMnet2, not NAT. This puts Windows behind the pfSense firewall instead of giving it direct internet access.

![Windows 11 ISO Selected](https://github.com/lionelmsango/pfsense-vmware-homelab/blob/5bf43e56bab2482bb5b500f2eae96ee48894b54e/Screenshots/06-test-vms-05-win11-vm-created.png.jpg)

---

#### Network Configuration - Windows 11

After Windows booted, I checked if it received an IP address from pfSense's DHCP server.

Opening Command Prompt and ran `ipconfig`. Perfect! Windows automatically got:
- IP address from pfSense DHCP (192.168.10.100)
- Correct subnet mask
- Default gateway pointing to pfSense (192.168.10.1)

Next, i tested internet connectivity, which worked as expected. The firewall was routing traffic correctly and performing NAT.

![Windows IP Address and internet test](https://github.com/lionelmsango/pfsense-vmware-homelab/blob/5bf43e56bab2482bb5b500f2eae96ee48894b54e/Screenshots/06-test-vms-10-win11-internet-test.png.jpg)

#### Accessing pfSense from Windows

From Windows, I opened Edge browser and navigated to `https://192.168.10.1`. I could access the login page, logged in succesfully and could manage the firewall from a client machine. 

![pfSense Access from Windows](https://github.com/lionelmsango/pfsense-vmware-homelab/blob/5bf43e56bab2482bb5b500f2eae96ee48894b54e/Screenshots/06-test-vms-11-win11-pfsense-access.png.jpg)


---

#### Setting Up Ubuntu Desktop

For my second test machine, I chose Ubuntu Desktop 24.04 LTS. This gave me a mix of Windows and Linux to test cross-platform scenarios.

---

#### Network Configuration - Ubuntu

I checked Ubuntu's network settings and confirmed it also received an IP from pfSense's DHCP server.

![Ubuntu Network Settings](https://github.com/lionelmsango/pfsense-vmware-homelab/blob/5bf43e56bab2482bb5b500f2eae96ee48894b54e/Screenshots/06-test-vms-15-ubuntu-network.png.jpg)

Next, went to Ubuntu terminal for some further tests. Ubuntu could access the internet through pfSense, proving that both operating systems were properly integrated into the network. Ubuntu could also access the pfSense web interface through Firefox, confirming full network connectivity.

![Ubuntu Internet Test](https://github.com/lionelmsango/pfsense-vmware-homelab/blob/5bf43e56bab2482bb5b500f2eae96ee48894b54e/Screenshots/06-test-vms-16-ubuntu-internet-test.png.jpg)
![Ubuntu pfSense Access](https://github.com/lionelmsango/pfsense-vmware-homelab/blob/5bf43e56bab2482bb5b500f2eae96ee48894b54e/Screenshots/06-test-vms-17-ubuntu-pfsense-access.png.jpg)

--

#### Cross-Platform Testing

i then tested to confirm that both operating systems could communicate with each other through the firewall. From Ubuntu, I could also ping Windows back. 

![Windows to Ubuntu Communication](https://github.com/lionelmsango/pfsense-vmware-homelab/blob/5bf43e56bab2482bb5b500f2eae96ee48894b54e/Screenshots/06-test-vms-18-win-to-ubuntu-ping.png.jpg)




































