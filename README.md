# smart-campus-network
Cisco Packet Tracer project — VLAN design, inter-VLAN routing, DHCP, ACLs and SSH security

🏫 Smart Campus Network Design

Tool: Cisco Packet Tracer
Level: Year 2 Network Engineering Portfolio Project
Status: ✅ Fully configured and verified


📋 Project Overview

A simulated smart campus network built in Cisco Packet Tracer demonstrating enterprise-level network design including VLAN segmentation, inter-VLAN routing using Router-on-a-Stick, centralised DHCP, Access Control Lists (ACLs), and router security hardening with SSH.

The project reflects a real-world campus environment with four departments — Admin, Student Lab, Library, and Server Room — each isolated in their own VLAN with controlled inter-department communication enforced by security policy.


🖧 Network Topology

                        Campus-Router (Cisco 2911)
                               |
                        Core-Switch (Cisco 2960)
                 ______________|______________
                |         |         |         |
          Switch-Admin  Switch-Lab  Switch-Lib  Switch-Server
          |        |    |   |   |   |      |       |
       PC-admin1  PC-admin2  PC-lab1~3   PC-lib1~2   Server0

DeviceModelRoleCampus-RouterCisco 2911Inter-VLAN routing, DHCP, ACLs, SSHCore-SwitchCisco 2960Central trunk switchSwitch-AdminCisco 2960Admin department access switchSwitch-LabCisco 2960Student Lab access switchSwitch-LibraryCisco 2960Library access switchSwitch-ServerCisco 2960Server room access switch


🔀 VLAN Design
```
VLAN IDNameSubnetGatewayDevices10Admin192.168.10.0/24192.168.10.1PC-admin1, PC-admin220Student-Lab192.168.20.0/24192.168.20.1PC-lab1, PC-lab2, PC-lab330Library192.168.30.0/24192.168.30.1PC-lib1, PC-lib240Server-Room192.168.40.0/24192.168.40.1Server0
```

All VLANs are configured on Core-Switch with trunk uplinks to the router and individual access switches.


🔁 Inter-VLAN Routing — Router-on-a-Stick

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

📡 DHCP Configuration

Centralised DHCP pools configured on the router serve all four VLANs automatically. Each PC is set to DHCP and receives the correct IP address, subnet mask, and default gateway for its VLAN.


🔒 Security Policy — ACLs

Policy Design

SourceDestinationActionReasonLab (20.x)Admin (10.x)❌ DENYProtect admin systems from studentsLibrary (30.x)Admin (10.x)❌ DENYProtect admin systems from studentsLab (20.x)Server (40.x)✅ PERMITStudents need server resourcesLibrary (30.x)Server (40.x)✅ PERMITStudents need server resourcesAdmin (10.x)All✅ PERMITIT staff need full access

ACL Implementation
```
ip access-list extended BLOCK_LAB_TO_ADMIN
 deny ip 192.168.20.0 0.0.0.255 192.168.10.0 0.0.0.255
 deny ip 192.168.10.0 0.0.0.255 192.168.20.0 0.0.0.255
 permit ip any any

interface GigabitEthernet0/0.20
 ip access-group BLOCK_LAB_TO_ADMIN in

ip access-list extended BLOCK_LIB_TO_ADMIN
 deny ip 192.168.30.0 0.0.0.255 192.168.10.0 0.0.0.255
 deny ip 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255
 permit ip any any

interface GigabitEthernet0/0.30
 ip access-group BLOCK_LIB_TO_ADMIN in

```




🔐 Router Security Hardening
```
! Encrypted enable password
enable secret Cisco@Campus1

! Console port secured
line console 0
 password Console@123
 login
 exec-timeout 5 0

! VTY lines — SSH only, Telnet disabled
line vty 0 4
 transport input ssh
 login local
 exec-timeout 5 0

! Local admin user with full privilege
username admin privilege 15 secret Admin@Campus1

! SSH configured
ip domain-name campus.local
crypto key generate rsa (1024-bit)

! All passwords encrypted
service password-encryption

! Login banner
banner motd # Authorized access only. All activity is monitored. #
```

✅ Verification Results

Inter-VLAN Routing


✅ PC-lab1 → 192.168.10.1 (Admin gateway): 4/4 replies
✅ All VLANs can reach Server-Room (192.168.40.x)
✅ All inter-VLAN pings successful


ACL Testing


✅ PC-lab1 → 192.168.10.2 (Admin PC): Blocked
✅ PC-lab1 → 192.168.40.x (Server): Permitted
✅ PC-lib1 → 192.168.10.x (Admin): Blocked
✅ PC-admin1 → all VLANs: Full access


SSH Verification


✅ SSH login from PC-admin1 to 192.168.10.1 successful
✅ Telnet blocked: "Connection refused by remote host"
✅ Passwords encrypted in running-config (type 5 and type 7)
✅ RSA key generated and confirmed



📁 Repository Structure

smart-campus-network/
├── README.md
├      
|
└── screenshots


🛠️ Skills Demonstrated
``
VLANs 802.1Q Trunking Router-on-a-Stick Inter-VLAN Routing DHCP
Named Extended ACLs SSH Telnet Blocking Password Encryption
Cisco IOS Cisco Packet Tracer Network Security Network Troubleshooting
```


📚 What I Learned


How VLANs segment a network at Layer 2 and why this matters for security
How Router-on-a-Stick uses 802.1Q sub-interfaces to route between VLANs on a single physical link
How named extended ACLs control east-west traffic between departments
How ACL direction (inbound vs outbound) affects which traffic is matched — discovered through live testing
How to harden a Cisco router with SSH, encrypted passwords, banners, and exec timeouts
How to verify every configuration using show commands and live traffic tests



Built as a Year 2 Network Engineering portfolio project.
