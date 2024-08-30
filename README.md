# How To Configuration Router Cisco IOS-XE 17.3.2

Full Demo Router IOS-XE and Cisco Switch vIOS :
---------------
- [Cisco Router IOS-XE 17.3.2 Routing OSPF, MPLS LDP, BGP and MPLS Service](https://www.youtube.com/playlist?list=PLy064HwEq9IzN4zMz1rtCb__-eBVGk_Gh)
- [Cisco Switch vIOS Configuration VLAN, Port Trunk and Port Access](https://youtube.com/playlist?list=PLy064HwEq9Iwrd5SHUB4PWZafL7lUcCJg&si=sDOHQqlU43nWHvMD)

Basic Configuration :
---------------
Basic Configuration
```
#Active Password Encryption
service password-encryption

#Configuration Hostname
hostname [HOSTNAME_DEVICES]

#Enable Password For Global Configuration
enable secret [PASSWORD]

#Configuration Username and Password For Login
username [USERNAME] privilege 15 secret [PASSWORD]

#Configuration Privillage Login
line vty 0 4
login local
transport input telnet ssh
transport output telnet ssh
```
Verification :
```
telnet [IP_ADDRESS]
ssh [USERNAME]@[IP_ADDRESS]
```

Interface Configuration :
---------------
Configuration Loopback
```
interface Loopback0
ip address [IP_ADDRESS] [NETMASK]
ip ospf network point-to-point
```
Configuration Port Interface
```
interface GigabitEthernet1
no shutdown
description "Link to Neighbor"
mtu [1998-9200]
ip address  [IP_ADDRESS] [NETMASK]
ip ospf authentication message-digest
ip ospf message-digest-key 1 md5 [PASSWORD]
ip ospf network point-to-point
ip ospf dead-interval 15
ip ospf hello-interval 5
ip ospf mtu-ignore
ip ospf cost [1-65000]
load-interval 30
negotiation auto
cdp enable
mpls ip
mpls label protocol ldp
```
Verification
```
show interface [PORT]
show interface [PORT] | include rate
show interface [PORT] | include error
show interface [PORT] | include CRC
show interface description | include up
show interface description | include down
show ip interface brief
show ip interface brief | include up
show ip interface brief | include down
```

Routing OSPF Configuration :
---------------
Configuration Routing OSPF Single Area (Internal Router) or Backbone Area (Area 0)
```
router ospf [OSPF-ID]
router-id [IP_LOOPBACK]
nsf cisco
passive-interface default <- Passive Port
no passive-interface GigabitEthernet1 <- Active Port
no passive-interface GigabitEthernet2 <- Active Port
network [IP_LOOPBACK] 0.0.0.0 area [OSPF_AREA]
network [IP_POINT_TO_POINT] 0.0.0.0 area [OSPF_AREA]
```
Configuration Routing OSPF Multiarea (Area Border Router)
```
router ospf [OSPF-ID]
router-id [IP_LOOPBACK]
nsf cisco
passive-interface default <- Passive Port
no passive-interface GigabitEthernet1 <- Active Port
no passive-interface GigabitEthernet2 <- Active Port
network [IP_LOOPBACK] 0.0.0.0 area [OSPF_AREA_A]
network [IP_POINT_TO_POINT] 0.0.0.0 area [OSPF_AREA_A]
network [IP_POINT_TO_POINT] 0.0.0.0 area [OSPF_AREA_B]
network [IP_POINT_TO_POINT] 0.0.0.0 area [OSPF_AREA_C]
```
Verification
```
show running | section router ospf
show ip ospf interface
show ip ospf interface brief
show ip ospf interface neighbor
```

MPLS LDP Configuration :
---------------
Configuration ACL For MPLS LDP
```
ip access-list standard ACL-MPLS-LDP
10 permit [IP_HOST_ALLOW]
20 permit [IP_HOST_ALLOW]
30 permit [IP_HOST_ALLOW]
```
Configuration MPLS LDP
```
mpls label protocol ldp
mpls ldp neighbor [IP_LOOPBACK_NEIGHBOR] password [PASSWORD]
mpls ldp graceful-restart
no mpls ldp advertise-labels
mpls ldp advertise-labels for ACL-MPLS-LDP
```
Verification
```
show mpls interface
show mpls ldp session
show mpls ldp neighbor
```

Routing BGP to Route Reflector :
---------------
Configuration NODE Client to Route Reflector
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
Configuration Route Reflector to NODE Client
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
Verification
```
show running | section router bgp
show bgp summary
```

Configuration Trunk to Service :
---------------
Configuration Trunk
```
interface GigabitEthernet2
no shutdown
description Trunk to Service
mtu 1900
no ip address
load-interval 30
negotiation auto
spanning-tree bpdufilter enable
```
verification
```
show interface [PORT_TRUNK]
show running interface [PORT_TRUNK]
```

MPLS Service :
---------------
MPLS L3VPN Service
```
Example :
#Configuration L3VPN and Service Instance

@NODE-A
ip vrf WAN-111
rd 65000:10100
route-target export 65000:10200
route-target import 65000:10100
!
router bgp 65000
address-family ipv4 vrf WAN-111
redistribute connected
redistribute static
exit-address-family
!
interface BDI111
description WAN-111
ip vrf forwarding WAN-111
ip address 10.100.0.1 255.255.255.252
no shutdown
!
service instance 111 ethernet
description MPLS_L3VPN
encapsulation dot1q 111
rewrite ingress tag pop 1 symmetric
bridge-domain 111

@NODE-B
ip vrf WAN-111
rd 65000:10100
route-target export 65000:10100
route-target import 65000:10200
!
router bgp 65000
address-family ipv4 vrf WAN-111
redistribute connected
redistribute static
exit-address-family
!
interface BDI111
description WAN-111
ip vrf forwarding WAN-111
ip address 10.200.0.1 255.255.255.252
no shutdown
!
service instance 111 ethernet
description MPLS_L3VPN
encapsulation dot1q 111
rewrite ingress tag pop 1 symmetric
bridge-domain 111

#Verification
show running interface bridge-domain [ID_SERVICE]
show ip interface brief
show running interface [PORT_TRUNK] | section [ID_SERVICE] ethernet
show bgp vpnv4 unicast vrf [VRF_LABEL]
ping vrf [VRF_LABEL] [IP_NEIGHBOR] 
traceroute vrf [VRF_LABEL] [IP_NEIGHBOR] 
```
VPLS (Virtual Private LAN Services)
```
Example :
#Configuration VPLS and Service Instance

@NODE-A
l2 vfi VFI-444 manual
vpn id 444
bridge-domain 444
mtu 1900
neighbor 11.11.11.11 encapsulation mpls
!
service instance 444 ethernet
description VPLS_SERVICE
encapsulation dot1q 444
rewrite ingress tag pop 1 symmetric
bridge-domain 444

@NODE-B
l2 vfi VFI-444 manual
vpn id 444
bridge-domain 444
mtu 1900
neighbor 21.21.21.21 encapsulation mpls
!
service instance 444 ethernet
description VPLS_SERVICE
encapsulation dot1q 444
rewrite ingress tag pop 1 symmetric
bridge-domain 444

#Verification
show running interface [PORT_TRUNK] | section [ID_SERVICE] ethernet
show mpls l2transport vc [VPLS-ID]
show vfi [VPLS-ID]
show bridge-domain [VPLS_ID]
```
MPLS L2VPN Pseudowire
```
Example :
#Configuration L2VPN and Service Instance

@NODE-A
service instance 666 ethernet
description MPLS_L2VPN
encapsulation dot1q 666
rewrite ingress tag pop 1 symmetric
bridge-domain 666
xconnect 11.11.11.11 666 encapsulation mpls

@NODE-B
service instance 666 ethernet
description MPLS_L2VPN
encapsulation dot1q 666
rewrite ingress tag pop 1 symmetric
bridge-domain 666
xconnect 21.21.21.21 666 encapsulation mpls

#Verification
show running interface [PORT_TRUNK] | section [ID_SERVICE] ethernet
show mpls l2transport vc [L2VPN-ID]
show bridge-domain [L2VPN_ID]
```
