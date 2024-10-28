+++
title = 'Overcoming IPv4 Private Address Overlaps in Large Business-to-Business Network Scenarios with Virtual Routing and Address Translations Using RFC 6598 Shared Address Space'
date = 2022-12-05
draft = false
+++

**Disclaimer**: _This is our final Networking paper during my master's degree in Information Security (InfoSec). The topic and initial router configurations were presented by my group mate, E.C. However, I had to make some changes to the provided router configurations and network architecture to remediate the issues we faced in the lab. I deployed the lab environment and revised router configuration using GNS3, then wrote our report paper, which you see below._

# Introduction
Over the years, the rapid exhaustion of Internet Protocol version 4 (IPv4) led to the development of private IPv4 addressing and Network Address Translation (NAT). RFC 1918 defined defined the Private IPv4 Address Space as a list of reserved addresses used for intra-enterprise communications. NAT, defined in RFC 3022, allows a host that does not have a valid, registered, and globally unique IP address to communicate with other hosts through the Internet by mapping the private address to a publicly routable address and vice versa. Different address mappings can also occur in NAT; one example can be translating an RFC 1918 address to an RFC 6598 address as demonstrated in this paper (post). NAT and private IPv4 addresses allow entities to reuse the same space for their internal networks. 

According to Harvard, the total Merger and Acquisition (M&A) deal volume in 2021 increased by more than 60% relative to the $3.6 trillion recorded total deal volume in 2020 -- a record-breaking activity. As businesses grow, connections and acquisitions occur to achieve synergy. Large organizations commonly use the 10.0.0.0/8 address space to accommodate the number of sites, subnets, and hosts while having enough room for growth. Connecting large business entities due to M&As having the same address range (i.e., 10.0.0.0/8) will not be able to establish proper network communications due to IPv4 address overlaps. The edge router will not be able to route to the remote network because a connected route entry to the same address space already exists in its routing table. A different business size using other address spaces (i.e., 172.16.0.0/12 and 192.168.0.0/16) can encouter a similar problem when merging with a remote network using the same private address range.

The Internet Assigned Numbers Authority (IANA) allocated the 100.64.0.0/10 address block defined in RFC 6598 as a non-globally routable Shared Address Space intended on the Service Provider (SP) networks or routing equipment capable of address translation across router interfaces having identical addresses on two different interfaces. The Shared Address Space enabled SPs to support IPv4 growth until the full deployment of Internet Protocol version 6 (IPv6) by using the 100.64.0.0/10 address block on the interfaces of Carrier-Grade NAT (CGN) devices connecting to the Cusomter Premises Equipment (CPE). Although it is possible to allocate the Shared Address Space to hosts in the private network when no RFC 1918 address is available, it increases the probability of address conflicts towards the SP network. Furthermore, it strays from the true intent specified in RFC 6598. Fig. 1 shows a sample SP topology leveraging the RFC 6598 addresses.

![RFC 6598 SP Topology](../../nat-rfc6598/rfc6598-sp-topology.png)
*Fig. 1. RFC 6598 SP Topology*

According to Google's IPv6 adoption statistics, the availability of IPv6 connectivity among Google users globally did not surpass 40% as of late 2022 since the beginning of the data in 2008. In addition, based on W3Tech survey, as of 2022, only 21.5% of websites use IPv6. IPv6, introduced in 1995, serves as a replacement protocol for IPv4. Even with the improvements that IPv6 brings, such as having a larger address space that supports 3.4x10^38, compared to 4.3 billion IPv4 unique addresses. Expensive infrastructure, no direct benefits for early trendsetters, adn end-user tech incompatibility contribute to the global adoption of IPv6. For these reasons, enterprises still run solely IPv4 in their network or as a dual-stack configuration.

With the depletion of IPv4 and the slow adoption of IPv6, merging business entities using the same private IPv4 address space will need a solution for their overlapping addresses. This study aims to explore and demonstrate the implementation of virtual routing and network address translations (NAT) using RFC 6598 Shared Address Space to overcome private IPv4 address overlaps in large business-to-business (B2B) network interconnections.

# Network Addressing Scheme and Address Conflicts

One of the considered best practice implementations of the IPv4 addressing scheme leverages the three RFC 1918 address blocks, 10.0.0.0/8, 172.16.0.0/12, and 192.168.0.0/16 for special-use networks. The 10.0.0.0/8 used in internal networks allocates the second octet of the address to distinguish the enterprise’s various sites (branch offices, data center, or VPN hub). The third octet of the address segments the site’s network into smaller subnetworks (subnets). The fourth and final octet of the address identifies the individual hosts residing in the specified subnet. The remaining address blocks, 172.16.0.0/12 and 192.168.0.0/16 are often used in Wide Area Network (WAN) links and Demilitarized Zone (DMZ), respectively. Note that the RFC 1918 does not mandate the specific use of each address block; the usage of each block can depend on the business requirements as long as used internally. 

![Network Proposal: Global Architecture](../../nat-rfc6598/network-propsal-global-architecture.png)
*Fig. 2. (a) Entity A Addressing Scheme (b) Entity B Addressing Scheme*

This study concentrates on two sampled enterprises, Entity A and Entity B, illustrated in Fig.2 (a) and (b), respectively. The sampled entities are global enterprises with thousands of employees across their sites throughout Asia, Europe, and the Americas. Both entities use the 10.0.0.0/8 Class A private network for their global intranet. The addressing scheme assigns a /16 network on each site; the second octet, 10.x.0.0/16, distinguishes the sites; this can accommodate 256 (2^8) total sites. In Fig. 2, the “x” in the 10.x.0.0/16 represents omitted sites for simplicity. The third octet further segments the /16 network of each site into a /24 subnet; each site can have 256 (2^8) separate subnets. For example, in Asia Office 2 (10.22.0.0/16) of Entity A, the Development VLAN can be assigned in the 10.22.0.0/24 subnet. Each subnet can allocate the remaining octet to identify the hosts; each subnet can have 254 hosts (2^8-2). The first IP address in the subnet, in this example, 10.22.0.0, identifies the subnet, and the last IP address, 10.22.0.255 in this example, acts as the broadcast address. A well-known practice seen throughout the Information Technology (IT) industry allocates the first usable IP address of the subnet to the default gateway; this can also be seen in Microsoft Azure default environments. Following the example above, the default gateway of the 10.22.0.0/24 subnet will have an IP address of 10.22.0.1. The remaining usable IP addresses in the subnet, in this example, addresses between 10.22.0.2 and 10.22.0.254, can be assigned to hosts in the subnet.

## Interconnecting Entity A and Entity B

With Entity A merging with Entity B, shown in Fig. 3, their internal network will overlap since both entities use the 10.0.0.0/8 address range. As a result, the routers connecting the two entities will not be able to properly route to the remote network due to addressing conflict since a connected route entry to the same subnet already exists in their routing table. 

![alt text](../../nat-rfc6598/global-entities-physical-interconnection.png)
*Fig. 3. Entity A and Entity B Physical Interconnection*

This post will focus on the scenario where a desktop in Entity A’s Asia Office 2 having an IP address of 10.22.0.10 requires bi-directional communication to a jump host residing in Entity B’s Data Center 2 (10.22.0.0/16) having an IP address of 10.22.0.20. The default gateway on both subnets utilizes the first usable IP address of the subnet, 10.22.0.1. A WAN link in the 172.16.0.0/30 subnet interconnects the Asia Office 2 of Entity A and the Data Center 2 of Entity B. The WAN link can be a site-to-site IPSec VPN, GRE, point-to-point, MPLS, or SD-WAN; the interconnection type between routers does not affect the implementation of virtual routing and NAT on the edge router. Therefore, this study abstracts the WAN link interconnection between entities for simplicity. 

The router deployed on both entities, running Cisco C7200 IOS, has two Fast Ethernet interfaces (F0/0 and F0/1). The WAN-facing FastEthernet0/0 interface interconnects the sites of the entities. FastEthernet0/1 faces the internal 10.22.0.0/24 subnet, acting as the default gateway. 

## Address Conflicts on the Routing Table
Routers select the best routes based on three criteria: Longest Prefix Match, Administrative Distance, and Metric. The route entry with the longest (most specific) prefix takes precedence over the other criteria. For example, if a router has 10.22.0.0/16 and 10.22.0.0/24 in its routing table, a packet destined for 10.22.0.20 will be forwarded using the 10.22.0.0/24 route. Administrative Distance (AD) measures the trustworthiness of the source of the routing information; the router uses the AD value to determine which routing protocol to use if two protocols provide route information for the same destination. Each routing protocol has its corresponding AD value shown in TABLE 1; the router chooses the lowest AD value when identifying which protocol to add to its routing table. When the router learns different paths to the same network from the same routing protocol, it consults the metric value to decide which route to add to the routing table. Similar to the AD, the router adds the route information with the lower metric in its routing table. The metric calculation varies from protocol to protocol; Routing Information Protocol (RIP) uses hop counts, OSPF uses a parameter called cost, et cetera.

|  Route Source (Protocol)  |  Default AD  | 
|----------|----------|
| Connected Interface   | 0 |
| Static Route  | 1 |
| Enhanced Interior Gateway Routing Protocol (EIGRP) Summary Route   | 5 |
| External Border Gateway Protocol (BGP)  | 20 |
| Internal EIGRP  | 90 |
| IGRP  | 100 |
| OSPF  | 110 |
| Intermediate System-to-Intermediate System (IS-IS)  | 115 |
| Routing Information Protocol (RIP)  | 120 |
| External Information Protocol (EGP)  | 140 |
| On Demand Routing  | 160 |
| External EIGRP  | 170 |
| Internal BGP  | 200 |
| Unknown  | 255 |

*TABLE 1 Default Administrative Distance Values*

TABLE 2 below the routing table on both entities’ routers. The router automatically adds a connected route and a local route into its routing table after enabling and configuring an IP address on the interface. The router checks the line status and protocol status of the interface. The line status represents Layer-2 connectivity, and the protocol status represents the Layer-3 (IP) configuration; both must be in the UP state before the router can forward any packets. The connected route designates the subnet where the interface belongs by computing the configured subnet value. While the local route entry represents a host route (/32) used to efficiently forward local traffic to the router’s Network Interface Card (NIC).  

|  Type  |  Subnet  | Egress Interface  |  AD  |
|----------|----------|----------|----------|
|  Connected  |  10.22.0.0/24  | FastEthernet0/1  |  default  |
|  Local  |  10.22.0.1/32  | FastEthernet0/1  |  default  |
|  Connected  |  172.16.0.0/30 | FastEthernet0/0  |  default  |
|  Local  |  172.16.0.1/32  | FastEthernet0/0  |  default  |

*(a) Entity A*

|  Type  |  Subnet  | Egress Interface  |  AD  |
|----------|----------|----------|----------|
|  Connected  |  10.22.0.0/24  | FastEthernet0/1  |  default  |
|  Local  |  10.22.0.1/32  | FastEthernet0/1  |  default  |
|  Connected  |  172.16.0.0/30 | FastEthernet0/0  |  default  |
|  Local  |  172.16.0.2/32  | FastEthernet0/0  |  default  |

*(b) Entity B*

*TABLE 2 (a) Entity A Routing Table (b) Entity B Routing Table*

In TABLE 2 above, the routers have identical routes for the 10.22.0.0/24 subnet. A static route to the 10.22.0.0/24 subnet of Entity B will conflict with the connected route entry. Furthermore, the router ignores the static route configuration since directly connected routes take precedence due to its lower AD value of 0. Although it is possible to create a longest prefix match static route to bypass the connected route for traffic destined to Entity B’s jump host (10.22.0.20/32), conflicts can still occur for hosts having identical IP addresses. For example, the jump host in Entity B has a static IP address of 10.22.0.10/32; a longest prefix match static route can no longer be a solution since it now conflicts with the desktop in Entity A having the same IP address of 10.22.0.10/32. Instead, virtual routing and NAT configured on the router of Entity A introduce a more scalable solution with far lesser possibility of addressing conflicts due to the use of the RFC 6598 Shared Address Space instead of a different RFC 1918 address block. The reason is that IANA did not intend to allocate the RFC 6598 Shared Address Space to hosts in the private network. The proposed solution will require minimal configuration on the router of Entity B. It will only need a routing entry to the translated address of Entity A. Making it viable for entities with limited access and control over the merging network devices. Furthermore, Virtual routing (VRF) and NAT are features available in most enterprise-grade routers used by global entities, minimizing cost when implementing this solution.

## GNS3 Emulated Environment

The Graphical Network Simulator-3 (GNS3) is an open-source network emulator used by thousands of network engineers and many large companies worldwide, including Fortune 500 companies, to emulate, configure, test, and troubleshoot virtual and real networks. GNS3 consists of two main components: the GNS3-all-in-one software (GUI) and the GNS3 virtual machine (VM).

The GNS3-all-in-one software is the client graphical user interface (GUI) component of GNS3 installed in the host device (Windows, MAC, or Linux) that allows users to create network topologies. The devices deployed in the GNS3-all-in-one software require a host server to run. GNS3 supports three options for its server: local GNS3 server, local GNS3 VM, and remote GNS3 VM. 

![GNS3 Architecture](../../nat-rfc6598/gns3-architecture.png)

*Fig. 4. GNS3 Architecture*

The local GNS3 server runs locally on the same host device as the GNS3-all-in-one software. The GNS3 VM can either be run locally on the host device using virtualization software like VMWare Workstation, VirtualBox, or Hyper-V; or provisioned remotely using VMWare ESXi or the cloud. This study selected the local GNS3 VM, as recommended by GNS3, to emulate the Cisco 7200 IOS using Dynamips, a MIPS Processor emulation program, and Linux Ubuntu container through QEMU, a generic and open-source emulator and virtualizer. Fig. 4 shows the GNS3 architecture in this paper; the GNS3 VM, running Ubuntu OS, runs on the VMWare Workstation hypervisor. The host device is a 64-bit Windows 10 laptop.

## Network configuration

![Simplified Physical Network Topology](../../nat-rfc6598/simplified-physical-network-topology.png)
*(a)*
![Simplified Logical Network Topology](../../nat-rfc6598/simplified-logical-network-topology.png)
*(b)*

*Fig. 5. (a) Simplified Physical Network Topology (b) Simplified Logical Network Topology*

Fig. 5 (a) illustrates the simplified physical network topology between Asia Office 2 of Entity A and Data Center 2 of Entity B. Fig. 5 (b) shows the logical diagram of the same network, including the virtual router and logical interfaces to be configured on Entity A’s router. All network address translation occurs at the router of Entity A. The only requirement for the router at Entity B is a route entry to the RFC 6598 translated address of Entity A via the WAN link. 

![GNS3 Proposed Network Topology](../../nat-rfc6598/gns3-proposed-network-topology.PNG)
*Fig. 6. GNS3 Proposed Network Topology*

Fig. 6 above shows the proposed emulated network topology of Fig. 5 on GNS3 using the aforementioned virtual appliances. A C7200-I/O-FE network adapter that houses two FastEthernet interfaces (F0/0 and F0/1) is attached to the Cisco routers to meet the requirements of the simplified network shown in Fig. 5 (a).

### Endpoint Network configuration

To truly emulate the network scenario, the endpoints on both entities must have the correct network configuration. This study utilizes the GUI settings of Linux Ubuntu to set the device name and network values. Table 3 below specifies the IPv4 address, subnet, and default gateway configuration of the ens3 Network Interface Card (NIC) on the endpoints of each business entity. The table also defines the hostname (device name) to distinguish the devices in the topology. Although this paper concentrates on Linux configuration, the endpoints can run a different OS (Windows or MAC) as long as it has the correct network settings.