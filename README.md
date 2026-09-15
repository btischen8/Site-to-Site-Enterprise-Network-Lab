# Site-to-Site-Enterprise-Network-Lab
This repo serves as documentation for a two-site corporate network environment I simulated in Cisco Packet Tracer. It consists of a Headquarters location and a Branch location, each with a three-tier campus architecture connected over a GRE tunnel across a simulated ISP. Many fundamental network services, security features, and routing capabilities were implemented along the way, all while verifying end-to-end host connectivity and network access were verifiable.

## Key Implementations
- **Addressing**
    A structured 10.0.0.0/8 scheme, subnetted by site and functionality. 203.0.113.0/24 block set for public       facing NAT over ISP.
- **Switching**
    VTPv2 Domains on each site, DTP disabled across network, dedicated native VLAN, LACP Etherchannels between distribution and core switches, and a dedicated shared VLAN for voice traffic.
- **Routing & Redundancy**
    A single area OSPF domain is implemented over each location, and HSRPv2 functionality is applied to each        distribution layer switch to provide gateway redundancy.
- **WAN & Security**
    A GRE Tunnel between edge routers is placed and functional with dynamic NAT.(IPSEC functionality not fully     implemented due to packet tracer limitations. documented @ design level). DHCP snooping, DAI, Port-            Security, SSH remote management, and targeted Access Control Lists all structured to enforce rigid network     system security measures.
## Design Methodology
The design followed a layered, least-privilege approach.Starting at the access layer, switches enforce host-based security through the implemenation of port security, DHCP Snooping and DAI. On the Distribution layer, VLAN Gateways and HSRPv2 Gateway redundancy provide no single point of error for outbound traffic. Switches on the Core layer provide a routed backbone acting strictly as Layer 3 switches. All devices have access to the DMZ (Server Network) linked to R1 at the headquarters branch.
## Topology

<img width="1887" height="718" alt="Screenshot 2026-09-15 015703" src="https://github.com/user-attachments/assets/cc9bc568-2c92-46cf-981c-b7d8c98fb133" />

## Services & Features
  -**IPSEC over GRETunnel**

GRE Tunnel providing connectivity across a simulated ISP between sites. IPSEC configuration not supported on this version of packet tracer due to securityk9 payload encryption limitations so i'll drop the hypothetical method of configurtations below
**R1:**
```
crypto isakmp policy 10
 encryption aes 128
 hash sha
 authentication pre-share
 group 2
 lifetime 3600
crypto isakmp key CHANGEME address 203.0.113.5

crypto ipsec transform-set TS esp-aes esp-sha-hmac
 mode transport

crypto ipsec profile GRE-PROTECT
 set transform-set TS

interface Tunnel0
 tunnel protection ipsec profile GRE-PROTECT

 -mirrored on R2
```
  -**HSRPv2**

Redundant gateway using a VIP on the distribution layer

  -**LACP Etherchannel**

Bundeled etherchannels providing load balancing between distribution and core layer switches

  -**Inter VLAN Routing**

SVIs configured to provide intra-vlan communications

  -**OSPF**

OSPF single-area dynamic routing providing reachability over the internet and between sites

  -**Dynamic NAT**

NAT pools configured on edge routers to provide inside to outside address mappings for each subnet

  -**DHCP Relay**

helper addresses configured at each subnet gateway to provide a relay for DHCP communications to the DHCP server

  -**DHCP Server**

Dedicated server to provide DHCP services for hosts

  -**DNS Server**

Dedicated server to provide domain names for hosts

  -**NTP Server**

Dedicated server to provide a central time source for the network

  -**Syslog Server**

Dedicated server for collecting and storing log messages

  -**HTTP Server**

Dedicated server for hosting the company web page

  -**Port-Security**

enabled on host facing interfaces on access switches with restrict mode enabled to drop unauthorized traffic from rogue clients

  -**DHCP Snooping**

enabled with rate limiting to deter DHCP flooding and man in the middle attacks

  -**Dynamic ARP Inspection**

enabled to check ARP requests against MAC tables to combat spoofing and man in the middle attempts.

  -**ACLs**

1. Both Management subnets can only be accessed by either Management subnets
2. Users should only be able to use neccessary services when accessing the server network

  -**SSH**

Configured and tested on DSW-B1 to simulate remote management
