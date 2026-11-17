
# Smart Campus Network Design

**Cisco Packet Tracer project: VLAN design, inter-VLAN routing, DHCP, ACLs and SSH security**

**Tool:** Cisco Packet Tracer
**Type:** Network Engineering portfolio project
**Status:** Fully configured and verified

---

## Project Overview

A simulated smart campus network built in Cisco Packet Tracer, demonstrating enterprise-style network design: VLAN segmentation, inter-VLAN routing using Router-on-a-Stick, centralised DHCP, Access Control Lists (ACLs), and router hardening with SSH.

The network models a campus with four departments (Admin, Student Lab, Library and Server Room). Each department sits in its own VLAN, and communication between them is controlled by security policy.

> **Note:** All credentials in this project are lab-only and have been removed from this README. Placeholders such as `<enable-secret>` are shown instead.

---

## Network Topology

```
                    Campus-Router (Cisco 2911)
                           |
                    Core-Switch (Cisco 2960)
             ______________|______________
            |         |         |         |
      Switch-Admin  Switch-Lab  Switch-Lib  Switch-Server
      |        |    |   |   |   |      |       |
   PC-admin1  PC-admin2  PC-lab1~3   PC-lib1~2   Server0
```

| Device | Model | Role |
|---|---|---|
| Campus-Router | Cisco 2911 | Inter-VLAN routing, DHCP, ACLs, SSH |
| Core-Switch | Cisco 2960 | Central trunk switch |
| Switch-Admin | Cisco 2960 | Admin department access switch |
| Switch-Lab | Cisco 2960 | Student Lab access switch |
| Switch-Library | Cisco 2960 | Library access switch |
| Switch-Server | Cisco 2960 | Server room access switch |

---

## VLAN Design

| VLAN ID | Name | Subnet | Gateway | Devices |
|---|---|---|---|---|
| 10 | Admin | 192.168.10.0/24 | 192.168.10.1 | PC-admin1, PC-admin2 |
| 20 | Student-Lab | 192.168.20.0/24 | 192.168.20.1 | PC-lab1, PC-lab2, PC-lab3 |
| 30 | Library | 192.168.30.0/24 | 192.168.30.1 | PC-lib1, PC-lib2 |
| 40 | Server-Room | 192.168.40.0/24 | 192.168.40.1 | Server0 |

All VLANs are configured on the Core-Switch, with trunk uplinks to the router and to each access switch.

---

## Inter-VLAN Routing (Router-on-a-Stick)

The router uses 802.1Q sub-interfaces on a single physical interface (Gi0/0) to route between all VLANs:

```
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0

interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0

interface GigabitEthernet0/0.30
 encapsulation dot1Q 30
 ip address 192.168.30.1 255.255.255.0

interface GigabitEthernet0/0.40
 encapsulation dot1Q 40
 ip address 192.168.40.1 255.255.255.0
```

---

## DHCP Configuration

Centralised DHCP pools on the router serve all four VLANs. Each PC is set to DHCP and receives the correct IP address, subnet mask and default gateway for its VLAN.

---

## Security Policy (ACLs)

### Policy Design

| Source | Destination | Action | Reason |
|---|---|---|---|
| Lab (VLAN 20) | Admin (VLAN 10) | DENY | Protect admin systems from student networks |
| Library (VLAN 30) | Admin (VLAN 10) | DENY | Protect admin systems from student networks |
| Lab (VLAN 20) | Server (VLAN 40) | PERMIT | Students need server resources |
| Library (VLAN 30) | Server (VLAN 40) | PERMIT | Students need server resources |
| Admin (VLAN 10) | All | PERMIT | IT staff need full access |

### ACL Implementation

```
ip access-list extended BLOCK_LAB_TO_ADMIN
 deny ip 192.168.20.0 0.0.0.255 192.168.10.0 0.0.0.255
 permit ip any any

interface GigabitEthernet0/0.20
 ip access-group BLOCK_LAB_TO_ADMIN in

ip access-list extended BLOCK_LIB_TO_ADMIN
 deny ip 192.168.30.0 0.0.0.255 192.168.10.0 0.0.0.255
 permit ip any any

interface GigabitEthernet0/0.30
 ip access-group BLOCK_LIB_TO_ADMIN in
```

---

## Router Security Hardening

```
! Encrypted enable password
enable secret <enable-secret>

! Console port secured
line console 0
 password <console-password>
 login
 exec-timeout 5 0

! VTY lines: SSH only, Telnet disabled
line vty 0 4
 transport input ssh
 login local
 exec-timeout 5 0

! Local admin user with full privilege
username admin privilege 15 secret <admin-password>

! SSH configuration
ip domain-name campus.local
crypto key generate rsa

! Encrypt all plain-text passwords
service password-encryption

! Login banner
banner motd # Authorized access only. All activity is monitored. #
```

---

## Verification Results

### Inter-VLAN Routing
- PC-lab1 to 192.168.10.1 (Admin gateway): 4/4 replies
- All VLANs can reach the Server Room (192.168.40.x)
- All permitted inter-VLAN pings successful

### ACL Testing
- PC-lab1 to 192.168.10.2 (Admin PC): **blocked**
- PC-lab1 to 192.168.40.x (Server): **permitted**
- PC-lib1 to 192.168.10.x (Admin): **blocked**
- PC-admin1 to all VLANs: full access

### SSH Verification
- SSH login from PC-admin1 to 192.168.10.1 successful
- Telnet blocked: "Connection refused by remote host"
- Passwords encrypted in running-config (type 5 and type 7)
- RSA key generated and confirmed

---

## Screenshots

| Description | File |
|---|---|
| Setup | `setup.png` |
| Router configuration | `campus_router.png` |
| Admin PCs | `pc_admin1.png`, `pc_admin2.png` |
| Lab PCs | `pc_lab1.png`, `pc_lab2.png`, `pc_lab3.png` |
| Library PC | `pc_lib1.png` |
| Server | `server0.png` |

---

## Repository Structure

```
smart-campus-network/
├── README.md
├── Campus.pkt          # Packet Tracer topology file
└── *.png               # Verification screenshots
```

---

## Skills Demonstrated

VLANs, 802.1Q trunking, Router-on-a-Stick, inter-VLAN routing, DHCP, named extended ACLs, SSH, Telnet blocking, password encryption, Cisco IOS, Cisco Packet Tracer, network security, network troubleshooting.

---

## What I Learned

- How VLANs segment a network at Layer 2 and why this matters for security
- How Router-on-a-Stick uses 802.1Q sub-interfaces to route between VLANs over a single physical link
- How named extended ACLs control traffic between departments
- How ACL direction (inbound vs outbound) changes which traffic is matched, which I discovered through live testing
- How to harden a Cisco router with SSH, encrypted passwords, banners and exec timeouts
- How to verify every configuration with `show` commands and live traffic tests

---

## Author

**Haoua Tamaivou**: Network Engineering student, cybersecurity focus
GitHub: [Queeninherprime](https://github.com/Queeninherprime)
