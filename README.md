# How To Configuration Router Cisco IOS-XE 17.3.2
Hi Engineer, I Create a Tutorial on How to Configure a Cisco Router Version IOS-XE 17.3.2. On this occasion I use PNETLAB with a laptop specification of Intel Core i5 Gen 10th 6-Core, 32GB RAM, 200GB Free Disk to run it.

If you experience problems, please contact via email: [anggarda.wijaya@outlook.com](mailto:anggarda.wijaya@outlook.com) or linkedin: [Anggarda Saputra Wijaya](https://www.linkedin.com/in/anggarda-wijaya410) Many Thanks.

Full Video Tutorial Cisco Router IOS-XE and Cisco Switch vIOS :
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

MPLS LDP Configuration :
---------------
Configuration MPLS LDP
```
mpls label protocol ldp
mpls ldp neighbor [IP_LOOPBACK_NEIGHBOR] password [PASSWORD]
mpls ldp graceful-restart
no mpls ldp advertise-labels
mpls ldp advertise-labels for ACL-MPLS-LDP
```

Access Control List For LDP :
---------------
Configuration ACL For MPLS LDP
```
ip access-list standard ACL-MPLS-LDP
10 permit [IP_HOST_ALLOW}
20 permit [IP_HOST_ALLOW}
30 permit [IP_HOST_ALLOW}
```

Routing BGP to Route Reflector :
---------------
Configuration Routing BGP to RR
```
router bgp [AS_NUMBER]
bgp router-id [IP_LOOPBACK]
bgp log-neighbor-changes
bgp graceful-restart
no bgp default ipv4-unicast
neighbor [IP_ROUTE_REFLECTOR] remote-as [AS_NUMBER_ROUTE_REFLECTOR]
neighbor [IP_ROUTE_REFLECTOR] description [TO_ROUTE_REFLECTOR]
neighbor [IP_ROUTE_REFLECTOR] password [PASSWORD]
neighbor [IP_ROUTE_REFLECTOR] update-source Loopback0
```
Activate BGP VPNV4
```
address-family vpnv4
neighbor [IP_ROUTE_REFLECTOR] activate
neighbor [IP_ROUTE_REFLECTOR] send-community both
exit-address-family
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

MPLS Service :
---------------
MPLS L3VPN Service
```
Example :
#Configuration Virtual Routing Forwarding
ip vrf WAN-111
rd 65000:10100
route-target export 65000:10200
route-target import 65000:10100

#Configuration Bridge-Domain Interface
interface BDI111
description WAN-111
ip vrf forwarding WAN-111
ip address 10.100.0.1 255.255.255.252
no shutdown

#Configuration Switch Virtual Interface
service instance 111 ethernet
description MPLS_L3VPN
encapsulation dot1q 111
rewrite ingress tag pop 1 symmetric
bridge-domain 111
```
VPLS (Virtual Private LAN Services)
```
Example :
#Configuration VPLS
l2 vfi VFI-444 manual
vpn id 444
bridge-domain 444
mtu 1900
neighbor 21.21.21.21 encapsulation mpls

#Configuration Switch Virtual Interface
service instance 444 ethernet
description VPLS_SERVICE
encapsulation dot1q 444
rewrite ingress tag pop 1 symmetric
bridge-domain 444
```
MPLS L2VPN Pseudowire
```
service instance 666 ethernet
description MPLS_L2VPN
encapsulation dot1q 666
rewrite ingress tag pop 1 symmetric
bridge-domain 666
xconnect 11.11.11.11 666 encapsulation mpls

```
