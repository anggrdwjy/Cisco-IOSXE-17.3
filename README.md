# How To Configuration Router Cisco IOS-XE 17.3

Basic Configuration :
---------------
Activate Password Encrypt
```
service password-encryption
```
Configuration Hostname
```
hostname [HOSTNAME_DEVICES]
```
Enable Password For Global Configuration
```
enable secret [PASSWORD]
```
Configuration Username and Password For Login
```
username [USERNAME] privilege 15 secret [PASSWORD]
```
Configuration Privillage Login
```
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

MPLS LDP Configuration :
---------------
Configuration MPLS LDP
```
mpls label protocol ldp
mpls ldp neighbor [IP_LOOPBACK_NEIGHBOR] password [PASSWORD
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

Routing OSPF Configuration :
---------------
Configuration Routing OSPF Single Area / Backbone
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
Configuration Routing OSPF Multiarea
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

Routing BGP :
---------------
router bgp 65000
bgp router-id 1.1.1.1
bgp log-neighbor-changes
bgp graceful-restart
no bgp default ipv4-unicast
neighbor 4.4.4.4 remote-as 65000
neighbor 4.4.4.4 description to-RR1
neighbor 4.4.4.4 password baseball
neighbor 4.4.4.4 update-source Loopback0
neighbor 6.6.6.6 remote-as 65000
neighbor 6.6.6.6 description to-RR2
neighbor 6.6.6.6 password baseball
neighbor 6.6.6.6 update-source Loopback0
!
address-family vpnv4
neighbor 4.4.4.4 activate
neighbor 4.4.4.4 send-community both
neighbor 6.6.6.6 activate
neighbor 6.6.6.6 send-community both
exit-address-family



 







