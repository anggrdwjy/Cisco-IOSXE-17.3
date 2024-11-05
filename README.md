## This repository is a guide for learn Cisco IOS-XE resources and references to practice configuration Router.

## Summary Of Repository
* [Command-References](#Command-References)
* [Basic Configuration](#Basic-Configuration)
  * [Hostname](#Hostname)
  * [Enable Password](#Enable-Password)
  * [Username and Password](#Username-and-Password)
  * [Privilege Login](#Privilege-Login)
  * [Password-Encryption](#Password-Encryption)
  * [Time Clock](#Time-Clock)
  * [FTP \(File Transfer Protocol](#File-Transfer-Protocol)
  * [Spanning Tree Protocol](#Spanning-Tree-Protocol)
  * [CDP \(Cisco Discovery Protocol)](#CDP-(Cisco-Discovery-Protocol))
  * [UDLP (Unidirectional Link Detection) Protocol](#UDLD-(Unidirectional-Link-Detection)-Protocol)
  * [SNMP \(Simple Network Management Protocol)](#SNMP-(Simple-Network-Management-Protocol))
  * [NTP \(Network Time Protocol)](#Network-Time-Protocol)
* [Interface](#Interface)
  * [Loopback](#Loopback)
  * [Interface](#Interface)
* [LACP \(Link Aggregation Control Protocol)](#LACP-(Link-Aggregation-Control-Protocol))
  * [LACP](#LACP) 
* [Static Route](#Static-Route)
  * [Static Routing](#Static-Routing)
* [Routing Interior](#Routing-Interior)
  * [OSPF \(Open Shortest Path First)](#OSPF-(Open-Shortest-Path-First))
* [MPLS LDP](#MPLS-LDP)
  * [MPLS LDP](#MPLS-LDP)
  * [ACL LDP](#ACL-LDP)
* [Routing Exterior](#Routing-Exterior)
  * [iBGP \(Interior) Example](#iBGP-(Interior)-Example)
* [SVI \(Switch Virtual Instance)](#SVI-(Switch-Virtual-Instance))
  * [Service Instance](#Service-Instance)
  * [VLAN Dot1.Q](#VLAN-Dot1.Q)
* [MPLS L3VPN](#MPLS-L3VPN)
  * [VRF (Virtual Routing Forwarding) Example](#VRF-(Virtual-Routing-Forwarding)-Example)
* [MPLS L2VPN](#MPLS-L2VPN)
  * [L2 Circuit](#L2-Circuit)
  * [VPLS (Virtual Private LAN Service)](#VPLS-(Virtual-Private-LAN-Service))
* [Switch Configuration](#Switch-Configuration)
  * [VLAN](#VLAN)
  * [Static Route](#Static-Route)
  * [Port Trunk](#Port-Trunk)
  * [Port Access](#Port-Access)
* [Additional Resources](#Additional-Resources)
  * [Books / Manuals](#Books-/-Manuals)
  * [Youtube Playlist](#Youtube-Playlist)
## Command References

| Command | Description |
| ---- | ----- |
| show running | show configuration in router |
| show running interface [PORT] | show configuration interface |
| show running \| section router ospf | show configuration ospf |
| show running \| section router bgp | show configuration bgp |
| show running interface [PORT] | show configuration interface |
| show running interface [PORT_TRUNK] \| section [ID_SERVICE] ethernet | show configuration service instance |
| show running interface bridge-domain [ID_SERVICE] | show configuration interface bridge-domain |
| show route table | show routing table |
| show vlan | show vlan configuration |
| show mac-address-table dynamic vlan [VLAN_ID] | show mac address in vlan |
| show interface [PORT_TRUNK] | show interface port trunk |
| show ip interface brief | show ip all interface |
| show etherchannel summary | etherchannel summary |
| show ip ospf interface | show ospf interface |
| show ip ospf interface brief | show ospf all interface |
| show ip ospf interface neighbor | show ospf neighbor connected |
| show bridge-domain [VPLS_ID] | show bridge-domain vpls |
| show bridge-domain [L2VPN_ID] | show bridge-domain l2vpn |
| show mpls interface | show mpls interface |
| show mpls ldp session | show ldp session |
| show mpls ldp neighbor | show ldp neighbor connected |
| show mpls l2transport vc [VPLS-ID] | show l2transport virtual connection vpls |
| show mpls l2transport vc [L2VPN-ID] | show l2transport virtual connection l2 |
| show vfi [VPLS-ID] | show vpls configuration |
| show bgp summary | show bgp all connected |
| show bgp vpnv4 unicast vrf [VRF_LABEL] | show vrf MP-BGP configuration |
| telnet [IP_ADDRESS] | telnet access to router |
| ssh [USERNAME]@[IP_ADDRESS] | ssh access to router |
| ping [IP_GATEWAY] | ping point to point |
| ping vrf [VRF_LABEL] [IP_NEIGHBOR] | ping vpn to destination |
| traceroute vrf [VRF_LABEL] [IP_NEIGHBOR] | traceroute vpn to destination |
| copy running startup-config | save configuration in startup router |
| write | save configuration |


# Basic Configuration
## Hostname
```
hostname [HOSTNAME]
```

## Enable Password
```
enable secret [PASSWORD]
```

## Username and Password
```
username [USERNAME] privilege 15 secret [PASSWORD]
```

## Privilege Login
```
line vty 0 4
login local
transport input telnet ssh
transport output telnet ssh

line vty 5 15
transport input none
transport output none
```

## Password-Encryption
```
service password-encryption
```
## Time Clock
```
clock timezone WIB 7 0
```

## FTP (File Transfer Protocol)
```
ip ftp source-interface Loopback0
ip ftp username [USERNAME]
ip ftp password 7 [PASSWORD]
```

## Spanning Tree Protocol
```
spanning-tree mode mst
spanning-tree loopguard default
spanning-tree logging
spanning-tree extend system-id
spanning-tree mst 0 priority 4096
```

## UDLD (Unidirectional Link Detection) Protocol
```
udld enable
```

## CDP (Cisco Discovery Protocol)
```
cdp run

interface Gi1
cdp enable
```

## SNMP (Simple Network Management Protocol)
```
snmp-server community [SNMP-COMMUNITY]
snmp-server trap-source Loopback0
snmp-server location [SITE-ID]
snmp-server contact [EMAIL@EMAIL.COM]
snmp-server host [IP_SERVER_SNMP]
```

## NTP (Network Time Protocol)
```
ntp source Loopback0
ntp server [IP_SERVER_NTP]
ntp server [IP_SERVER_NTP]
```

# Interface
## Loopback
Loopback Configuration
```
interface Loopback0
ip address [IP_ADDRESS] [NETMASK]
```
Loopback OSPF Additional
```
interface Loopback0
ip ospf network point-to-point
```
## Interface
Interface Configuration
```
interface GigabitEthernet1
no shutdown
description [DESCRIPTION]
mtu [1500-9200]
ip address  [IP_ADDRESS] [NETMASK]
load-interval 30
negotiation auto
cdp enable
```
Interface OSPF Additional
```
interface GigabitEthernet1
ip address  [IP_ADDRESS] [NETMASK]
ip ospf authentication message-digest
ip ospf message-digest-key 1 md5 [PASSWORD]
ip ospf network point-to-point
ip ospf dead-interval 15
ip ospf hello-interval 5
ip ospf mtu-ignore
ip ospf cost [1-65000]
```
Interface MPLS LDP Additional
```
interface GigabitEthernet1
mpls ip
mpls label protocol ldp
mpls traffic-eng tunnels
ip rsvp bandwidth
```

# LACP (Link Aggregation Control Protocol)
## LACP 
Port Channel Configuration
```
interface Port-channel1
ip address 192.168.1.1 255.255.255.0

interface GigabitEthernet0/1
switchport
switchport mode trunk
channel-group 1 mode active

interface GigabitEthernet0/2
switchport
switchport mode trunk
channel-group 1 mode active
```

# Static Route
## Static Routing
```
ip route 0.0.0.0 0.0.0.0 [GATEWAY]
ip route [NETWORK_NEIGHBOR] [PREFIX] [GATEWAY]
```
Optional
```
ip default-gateway [GATEWAY]
```

# Routing Interior
## OSPF (Open Shortest Path First)
Routing OSPF Single Area / Backbone Area
```
router ospf [OSPF-ID]
router-id [IP_LOOPBACK]
network [IP_LOOPBACK] 0.0.0.0 area [OSPF_AREA]
network [IP_POINT_TO_POINT] 0.0.0.0 area [OSPF_AREA]
```
Routing OSPF Multiarea
```
router ospf [OSPF-ID]
router-id [IP_LOOPBACK]
network [IP_LOOPBACK] 0.0.0.0 area [OSPF_AREA_A]
network [IP_POINT_TO_POINT] 0.0.0.0 area [OSPF_AREA_A]
network [IP_POINT_TO_POINT] 0.0.0.0 area [OSPF_AREA_B]
network [IP_POINT_TO_POINT] 0.0.0.0 area [OSPF_AREA_C]
```
OSPF Additional
```
router ospf [OSPF-ID]
nsf cisco
passive-interface default <- Passive Port
no passive-interface GigabitEthernet1 <- Active Port
no passive-interface GigabitEthernet2 <- Active Port
mpls ldp sync
mpls traffic-eng router-id Loopback0
mpls traffic-eng area 262
```

# MPLS LDP
## MPLS LDP
MPLS LDP Configuration
```
mpls label protocol ldp
mpls ldp graceful-restart
no mpls ldp advertise-labels
no mpls ip propagate-ttl 
mpls traffic-eng tunnels
xconnect logging pseudowire status
xconnect logging redundancy
mpls ldp neighbor [IP_LOOPBACK_NEIGHBOR] password [PASSWORD]
mpls ldp neighbor [IP_LOOPBACK_NEIGHBOR] password [PASSWORD]
```
## ACL LDP
Access Control List LDP
```
ip access-list standard ACL-MPLS-LDP
10 permit [IP_HOST_ALLOW]
20 permit [IP_HOST_ALLOW]
30 permit [IP_HOST_ALLOW]
mpls ldp advertise-labels for ACL-MPLS-LDP
```

# Routing Exterior
## iBGP (Interior) Example
BGP Configuration to Route Reflector
```
router bgp [AS_NUMBER]
bgp router-id [IP_LOOPBACK]
bgp log-neighbor-changes
bgp graceful-restart
no bgp default ipv4-unicast
neighbor [IP_ROUTE_REFLECTOR] remote-as [AS_NUMBER]
neighbor [IP_ROUTE_REFLECTOR] description [TO_ROUTE_REFLECTOR]
neighbor [IP_ROUTE_REFLECTOR] password [PASSWORD]
neighbor [IP_ROUTE_REFLECTOR] update-source Loopback0
!
address-family vpnv4
neighbor [IP_ROUTE_REFLECTOR] activate
neighbor [IP_ROUTE_REFLECTOR] send-community both
exit-address-family
```
BGP Configuration to Route Reflector Client
```
router bgp [AS_NUMBER]
bgp router-id [IP_LOOPBACK_RR]
bgp log-neighbor-changes
bgp graceful-restart
no bgp default ipv4-unicast
neighbor RR-CLIENT peer-group
neighbor RR-CLIENT remote-as [AS_NUMBER]
neighbor RR-CLIENT password [PASSWORD]
neighbor RR-CLIENT update-source Loopback0
neighbor [IP_RR_CLIENT_A] peer-group RR-CLIENT
neighbor [IP_RR_CLIENT_B] peer-group RR-CLIENT
neighbor [IP_RR_CLIENT_B] peer-group RR-CLIENT
!
address-family vpnv4
bgp slow-peer detection
neighbor RR-CLIENT send-community both
neighbor RR-CLIENT route-reflector-client
neighbor RR-CLIENT slow-peer split-update-group dynamic
neighbor [IP_RR_CLIENT_A] activate
neighbor [IP_RR_CLIENT_B] activate
neighbor [IP_RR_CLIENT_C] activate
exit-address-family
```

# SVI (Switch Virtual Instance)
## Service Instance
```
interface GigabitEthernet2
no shutdown
description Trunk to Service
mtu 1900
no ip address
load-interval 30
negotiation auto
spanning-tree bpdufilter enable
service instance 111 ethernet
description MPLS_L3VPN
encapsulation dot1q 111
rewrite ingress tag pop 1 symmetric
bridge-domain 111
```
## VLAN Dot1.Q
```
interface GigabitEthernet2.111
description Trunk to Service
mtu 1900
no ip address
load-interval 30
negotiation auto
spanning-tree bpdufilter enable
xconnect 11.11.11.11 666 encapsulation mpls
```

# MPLS L3VPN
## VRF (Virtual Routing Forwarding) Example
VPN Instance Configuration
```
ip vrf WAN-111
rd 65000:10100
route-target export 65000:10100
route-target import 65000:10100
```
MP-BGP Configuration
```
router bgp 65000
address-family ipv4 vrf WAN-111
redistribute connected
redistribute static
exit-address-family
```
Bridge Domain Interface
```
interface BDI111
description WAN-111
ip vrf forwarding WAN-111
ip address 10.100.0.1 255.255.255.252
ip address 10.100.1.1 255.255.255.252 secondary
no shutdown
```
Service Instance
```
interface GigabitEthernet2
service instance 111 ethernet
description MPLS_L3VPN
encapsulation dot1q 111
rewrite ingress tag pop 1 symmetric
bridge-domain 111
```

# MPLS L2VPN
## L2 Circuit
Service Instance
```
interface GigabitEthernet2
service instance 666 ethernet
description MPLS_L2VPN
encapsulation dot1q 666
rewrite ingress tag pop 1 symmetric
bridge-domain 666
xconnect 11.11.11.11 666 encapsulation mpls
```

## VPLS (Virtual Private LAN Service)
VPLS Configuration
```
l2 vfi VFI-444 manual
vpn id 444
bridge-domain 444
mtu 1900
neighbor 21.21.21.21 encapsulation mpls
```
Service Instance 
```
interface GigabitEthernet2
service instance 444 ethernet
description VPLS_SERVICE
encapsulation dot1q 444
rewrite ingress tag pop 1 symmetric
bridge-domain 444
```

# Switch Configuration
## VLAN
VLAN Configuration
````
vlan [VLAN_ID]
vlan name [VLAN_NAME]
````
Interface VLAN
```
interface vlan [VLAN_ID]
description [SERVICE_NAME]
ip address [IP_ADDRESS] [NETMASK]
no shutdown
```

## Static Route
```
ip route 0.0.0.0 0.0.0.0 [GATEWAY]
ip route [NETWORK_NEIGHBOR] [PREFIX] [GATEWAY]
```

## Port Trunk
```
interface GigabitEthernet [PORT_TRUNK]
description Trunk to 11-CE
switchport trunk allowed vlan 111,444,666
switchport trunk encapsulation dot1q
switchport mode trunk
cdp enable
mtu 1900
load-interval 30
negotiation auto
udld port disable
spanning-tree bpdufilter enable
ip dhcp snooping trust -> Optional
```

## Port Access
```
interface GigabitEthernet [PORT_ACCESS]
description Access to A-CPE
switchport access vlan 444
switchport mode access
cdp enable
mtu 1900
load-interval 30
negotiation auto
udld port disable
spanning-tree bpdufilter enable
ip dhcp snooping trust -> Optional
```

# Additional Resources
## Books / Manuals
  
## Youtube Playlist
- [Cisco Router IOS-XE 17.3.2 Routing OSPF, MPLS LDP, BGP and MPLS Service](https://www.youtube.com/playlist?list=PLy064HwEq9IzN4zMz1rtCb__-eBVGk_Gh)
- [Cisco Switch vIOS Configuration VLAN, Port Trunk and Port Access](https://youtube.com/playlist?list=PLy064HwEq9Iwrd5SHUB4PWZafL7lUcCJg&si=sDOHQqlU43nWHvMD)
