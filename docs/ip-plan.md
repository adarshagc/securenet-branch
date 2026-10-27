# SecureNet Branch: IP Addressing Plan

## 1. Addressing Convention

IP address format: 10.<site>.<VLAN>.<host>

* Site 10: Head Office
* Site 20: Branch Office
* VLAN 10: Staff
* VLAN 20: Servers
* VLAN 30: Guest
* VLAN 99: Management
* Host .1: Default gateway for each LAN subnet

The addressing plan uses private IPv4 addresses to organize devices by site and VLAN.

## 2. VLANs and Subnets

| Site        | VLAN ID | Name    | Subnet        | Default Gateway |
| ----------- | ------: | ------- | ------------- | --------------- |
| Head Office |      10 | STAFF   | 10.10.10.0/24 | 10.10.10.1      |
| Head Office |      20 | SERVERS | 10.10.20.0/24 | 10.10.20.1      |
| Head Office |      30 | GUEST   | 10.10.30.0/24 | 10.10.30.1      |
| Head Office |      99 | MGMT    | 10.10.99.0/24 | 10.10.99.1      |
| Branch      |      10 | STAFF   | 10.20.10.0/24 | 10.20.10.1      |
| Branch      |      30 | GUEST   | 10.20.30.0/24 | 10.20.30.1      |
| Branch      |      99 | MGMT    | 10.20.99.0/24 | 10.20.99.1      |

## 3. WAN Addressing

The Head Office router and Branch router communicate over a point-to-point WAN link.

| Parameter                 | Value           |
| ------------------------- | --------------- |
| WAN subnet                | 172.16.0.0/30   |
| Subnet mask               | 255.255.255.252 |
| Head Office router WAN IP | 172.16.0.1      |
| Branch router WAN IP      | 172.16.0.2      |
| Network address           | 172.16.0.0      |
| Broadcast address         | 172.16.0.3      |

The /30 subnet provides two usable IPv4 addresses, one for each router interface.

## 4. Static IP Address Assignments

| Device              | IP Address  | Purpose                         |
| ------------------- | ----------- | ------------------------------- |
| HO-R1               | 10.10.10.1  | Staff VLAN gateway              |
| HO-R1               | 10.10.20.1  | Servers VLAN gateway            |
| HO-R1               | 10.10.30.1  | Guest VLAN gateway              |
| HO-R1               | 10.10.99.1  | Management VLAN gateway         |
| HO-SW1              | 10.10.99.2  | Switch management               |
| DNS Server          | 10.10.20.10 | Domain Name System              |
| Web Server          | 10.10.20.11 | Web hosting using Nginx         |
| Syslog / IDS Server | 10.10.20.12 | Security logging and monitoring |

The server addresses are reserved for implementation in Phase 3. The Syslog and IDS services will be developed in later project phases.

## 5. DHCP Address Ranges

DHCP will automatically assign IP configuration to client devices in the Head Office Staff and Guest VLANs.

| VLAN  | Excluded Address Range | Dynamic Address Range    |
| ----- | ---------------------- | ------------------------ |
| STAFF | 10.10.10.1–10.10.10.10 | 10.10.10.11–10.10.10.254 |
| GUEST | 10.10.30.1–10.10.30.10 | 10.10.30.11–10.10.30.254 |

The excluded addresses are reserved for gateways, infrastructure, and other devices that may require fixed IP assignments.

DHCP pools will be configured during the network implementation phase.

## 6. Switch Port Assignments — HO-SW1

| Switch Port | Mode   | VLAN           | Purpose         |
| ----------- | ------ | -------------- | --------------- |
| Fa0/1       | Trunk  | 10, 20, 30, 99 | Uplink to HO-R1 |
| Fa0/2–10    | Access | 10             | Staff devices   |
| Fa0/11–15   | Access | 20             | Servers         |
| Fa0/16–20   | Access | 30             | Guest devices   |

The trunk link carries traffic for multiple VLANs between the switch and router.

VLAN 99 is designated as the native VLAN for the trunk. The native VLAN configuration must match on both ends of the link.

## 7. Design Decisions

### Separate VLANs and Subnets

Each VLAN uses a separate IP subnet to divide the network into logical segments and control inter-VLAN communication.

### /24 LAN Subnets

A /24 subnet uses the subnet mask 255.255.255.0 and provides 254 usable IPv4 addresses. This is suitable for the project's LAN segments and simplifies address management.

### /30 WAN Subnet

A /30 subnet provides two usable IPv4 addresses for the point-to-point connection between the Head Office and Branch routers.

### Management VLAN

VLAN 99 is reserved for network management. It separates management traffic from ordinary user traffic.

### Guest Network Isolation

The Guest VLAN is separated from Staff and Servers VLANs. Access Control Lists (ACLs) will be implemented in Phase 4 to restrict unauthorized access to internal resources.

### Static and Dynamic Addressing

Infrastructure devices and servers use reserved static addresses, while supported client devices receive their IP configuration through DHCP.

## 8. Security Considerations

* Separate Staff, Servers, Guest, and Management traffic using VLANs.
* Apply ACLs to control communication between VLANs.
* Restrict management access to authorized administrators.
* Prevent Guest devices from accessing protected internal resources.
* Use static IP addresses for infrastructure devices and servers.
* Configure trunk ports carefully and verify native VLAN consistency.
* Implement and test security policies during the later project phases.

## 9. Project Implementation Plan

| Phase   | Planned Activity                                         |
| ------- | -------------------------------------------------------- |
| Phase 1 | Network planning and topology design                     |
| Phase 2 | Network implementation and connectivity testing          |
| Phase 3 | Ubuntu server setup, DNS, and Nginx web server           |
| Phase 4 | ACLs, network security, logging, and intrusion detection |

---

Project: SecureNet Branch
Document: IP Addressing Plan
Purpose: Define the network addressing scheme before implementation in Cisco Packet Tracer.
