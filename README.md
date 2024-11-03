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
```

## Password-Encryption
```
service password-encryption
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
```

# LACP
## LACP

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
```

# MPLS LDP
## MPLS LDP
MPLS LDP Configuration
```
mpls label protocol ldp
mpls ldp graceful-restart
no mpls ldp advertise-labels
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
