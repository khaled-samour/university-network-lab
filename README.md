# University Network Lab

A Cisco Packet Tracer project connecting four university departments to a shared server network through a central router.

Built by **Khaled Samour** for COMP 3115, Practical Network Principles, at UCAS Gaza. The project was completed and tested by the author within the practical exam's allotted time.

## Overview

The Al-Majd University scenario brings together Engineering, Computing, Library, and Management. Each department has its own router, access switch, and two PCs. Point-to-point WAN links connect the departments to a core router and a central server room.

The project demonstrates:

- IPv4 subnetting with `/24` LANs and `/30` WAN links.
- Single-area OSPF routing across five routers.
- Centralized DHCP with relay from department networks.
- DNS, web, and FTP services.
- Extended ACLs for department-specific access restrictions.
- SSH version 2 settings on the routers.

## Topology

| Component | Quantity | Role |
| --- | --- | --- |
| Cisco 1941 routers | 5 | Four department routers and one core router |
| Cisco 2960 switches | 5 | Department access and server connectivity |
| PCs | 8 | Two clients per department |
| Server-PT devices | 4 | DHCP, DNS, web, and FTP roles |

The topology contains 21 links and an additional power-distribution object. Department-to-core connections use serial WAN interfaces on the selected router models.

## Address plan

| Segment | Subnet | Gateway |
| --- | --- | --- |
| Engineering | `10.10.1.0/24` | `10.10.1.1` |
| Computing | `10.10.2.0/24` | `10.10.2.1` |
| Library | `10.10.3.0/24` | `10.10.3.1` |
| Management | `10.10.4.0/24` | `10.10.4.1` |
| Server room | `10.10.5.0/24` | `10.10.5.1` |

### WAN links

| Connection | Subnet | Department endpoint | Core endpoint |
| --- | --- | --- | --- |
| Engineering to core | `10.0.1.0/30` | `10.0.1.1` | `10.0.1.2` |
| Computing to core | `10.0.2.0/30` | `10.0.2.1` | `10.0.2.2` |
| Library to core | `10.0.3.0/30` | `10.0.3.1` | `10.0.3.2` |
| Management to core | `10.0.4.0/30` | `10.0.4.1` | `10.0.4.2` |

## Routing

The routers use **OSPF process 1, area 0** in their running configurations. The configuration covers department LANs, point-to-point WAN links, and the server LAN.

A central core-and-branch layout keeps the relationship between department networks and shared services easy to follow. The topology is intended as a learning lab, not a redundant production design.

## Shared services

| Service | Server address | Purpose |
| --- | --- | --- |
| DHCP | `10.10.5.5` | Departmental address pools |
| DNS | `10.10.5.10` | Lab name resolution |
| Web | `10.10.5.11` | Al-Majd University welcome page |
| FTP | `10.10.5.12` | Lab file-transfer service |

### DHCP relay

Each department router has `ip helper-address 10.10.5.5` on its LAN-facing interface. This forwards client DHCP requests to the central server rather than requiring a DHCP server in each department.

The server has separate Engineering, Computing, Library, and Management pools. Each pool specifies its department gateway and uses `10.10.5.10` for DNS.

### DNS and web

The DNS server maps:

| Name | Address |
| --- | --- |
| `www.almajd.edu` | `10.10.5.11` |
| `ftp.almajd.edu` | `10.10.5.12` |

The web server contains an Al-Majd University welcome page. These domain names are configured inside the simulation; they do not imply ownership of an Internet domain.

### FTP

The designated FTP server supports the lab's file-transfer scenario. Department ACLs restrict selected paths to its TCP control port. Credentials are not listed in this README.

## Access-control design

The router running configurations include these extended ACL rules:

| Source department | Configured restriction | Other traffic |
| --- | --- | --- |
| Library | Deny TCP port 21 to the designated FTP server, `10.10.5.12` | Permit other IP traffic |
| Engineering | Deny IP traffic to Management, `10.10.4.0/24` | Permit other IP traffic |
| Management | Deny TCP port 21 to any destination | Permit other IP traffic |

These are the rules in the project, not a claim of complete network isolation. In particular, the Management rule blocks FTP control traffic; it is not an HTTP-only allowlist. The Library rule targets the designated FTP server rather than every possible FTP destination.

## Open and explore

1. Install Cisco Packet Tracer from [Cisco Networking Academy](https://www.netacad.com/courses/getting-started-cisco-packet-tracer).
2. Download the repository's `.pkt` file.
3. In Packet Tracer, choose **File > Open** and select the file.
4. Allow the simulated network to settle.
5. Explore the router configurations, department networks, and server services.

The file records Packet Tracer version **9.0.0.0810**. Compatibility with other versions is not guaranteed.

Useful commands for exploring the routers include:

```text
show ip interface brief
show ip route
show ip ospf neighbor
show access-lists
```

Use the PCs to explore DHCP assignment, browse the lab website, and compare permitted and restricted traffic paths. Save device configurations before a controlled restart if you want running-configuration changes to persist.

## Learning focus

This project connects several networking concepts in one scenario: planning subnets, routing between remote LANs, relaying DHCP requests, mapping service names to addresses, and applying access rules close to department sources.

It is an educational simulation, not a production-ready deployment or security certification. Lab passwords must never be reused on real accounts or devices.
