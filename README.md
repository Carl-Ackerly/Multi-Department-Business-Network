# Multi-Department Business Network

A Cisco Packet Tracer lab connecting Management, Sales, and It departments using VLANs, inter-VLAN routing , DHCP, and access controls.

## Objectives 

- Seperate departments into individual VLANs.
- Allow communication between departments through a router.
- Automatically assign PC network settings using DHCP.
- Restrict SSH administration to the Management subnet.
- Configure and test switch port security.

## Equipment

- 1 Cisco 2911 router: R1
- 1 Cisco 2960 switch: S1
- 6 department PCs
- 1 disconnected TEST-PC for port-security testing
- Cisco Packet Tracer 9.0.0

## Network Topology

![Network topology](screenshots/topology.png)

R1 Gi0/0 connects to the switchs's Gi0/1 through an 802.1Q trunk carring VLANs 10, 20, and 30.

## VLAN and Addressing Plan

| Department | VLAN | Subnet          | Default Gateway | Switch Ports |
|---|---|---|---|---|
| Management |  10  | 192.168.10.0/24 | 192.168.10.1    |   Fa0/1-2    |
| Sales      |  20  | 192.168.20.0/24 | 192.168.20.1    |   Fa0/3-4    |
| IT         |  30  | 192.168.30.0/24 | 192.168.30.1    |   Fa0/5-6    |

The switch uses 192.168.10.2 on VLAN 10 for management. 

## Configuration Summary 

## Inter-VLAN Routing

R1 uses router-on-a-stick with one subinterfce per VLAN.
Each subinterface provices the default gateway for its subnet. 

### DHCP

R1 provicdes a separate DHCP pool for each department. 
Addresses .1 through .10 are exclueded in each subnet to reserve them for gateways
and infrastructure. 

### SSH Access Control

SSH version 2 and a local administrator account are configured on the router and switch.

The MGMT-ONLY standard ACL permits 192.168.10.0/24 and is applied inbound to ther VTY
lines with access-class. 

This restricts remote administration while preserving normal traffic between departments. 

### Port Security 

Switch ports Fz0/1 through Fa0/6 use:

- A maximum of one secure MAC address per port
- Sticky MAC address learning
- Shutdown mode for security violations

## How to Open

Download Multi-Department-Business-Network.pkt and open in Cisco Packet Tracer 9.0.0.

## Scope

This lab demostrates an internal business network. 
Internet connectivity is not configured. Inter-VLAN traffic is allowed;
the ACL restricts device administration. 


---

# Project 2: VLSM and Subnetting Upgrade

The original network is now being expanded by redesigning the IP addressing scheme using VLSM (Variable Length Subnet Masking).

Instead of using three separate /24 networks, the departments now use subnets created from the 192.168.10.0/24 parent network.

This upgrade uses different subnet sizes based on the needs of each department while maintaining VLAN separation, inter-VLAN routing, DHCP, and secure management access.


### Updated Network Topology

![VLSM Addressing Topology](screenshots/vlsm-addressing-topology.png)

The updated topology uses VLSM to divide the 192.168.10.0/24 parent network into separate subnets for Sales, IT, and Management.


### VLSM Addressing Plan

| Department | VLAN | Subnet           | Default Gateway | Switch Ports |
|---|---|---|---|---|
| Sales      |  20  | 192.168.10.0/26  | 192.168.10.1    | Fa0/3-4      |
| IT         |  30  | 192.168.10.64/27 | 192.168.10.65   | Fa0/5-6      |
| Management |  10  | 192.168.10.96/28 | 192.168.10.97   | Fa0/1-2      |

The switch management interface was updated to 192.168.10.98/28 on VLAN 10.

### Configuration Changes

The VLSM upgrade required several changes to the existing network configuration:

- Updated the router subinterfaces with the new subnet gateways and subnet masks.
- Reconfigured the DHCP pools so each VLAN receives addresses from its new subnet.
- Updated the switch management interface to 192.168.10.98/28.
- Updated the MGMT-ONLY ACL to permit only the Management subnet (192.168.10.96/28).
- Renewed the DHCP addresses on all department PCs.
- Verified inter-VLAN communication after the addressing changes.
- Tested SSH access to confirm Management devices were allowed while Sales devices were denied.

### Testing and Verification

After the VLSM upgrade, the network was tested to verify that the new addressing scheme worked correctly.

- All department PCs successfully received new IP addresses through DHCP.
- Devices were able to communicate across VLANs using inter-VLAN routing.
- The switch management interface was reachable from the Management subnet.
- SSH access from the Management network was successful.
- SSH access from the Sales network was denied as intended by the MGMT-ONLY ACL.
- Router and switch configurations were saved after successful testing.