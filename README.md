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

- 
