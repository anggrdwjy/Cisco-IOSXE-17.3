# How To Configuration 

Basic Configuration :
---------------
Activate Password Encrypt
```
service password-encryption
```
Create Hostname
```
hostname [HOSTNAME DEVICES]
```
Enable Password For Global Configuration
```
enable secret [PASSWORD]
```
Create Username and Password For Login
```
username [USER] privilege 15 secret [PASSWORD]
```
Create Privillage Login
```
line vty 0 4
login local
transport input telnet ssh
transport output telnet ssh
```

Interface Configuration :
---------------
interface Loopback0
 ip address 1.1.1.1 255.255.255.255
 ip ospf network point-to-point
!
interface GigabitEthernet1
 description Link to 10-PE
 mtu 8900
 ip address 172.0.1.2 255.255.255.252
 ip ospf authentication message-digest
 ip ospf message-digest-key 1 md5 7 1415131809062B2728
 ip ospf network point-to-point
 ip ospf dead-interval 15
 ip ospf hello-interval 5
 ip ospf mtu-ignore
 ip ospf cost 1
 load-interval 30
 negotiation auto
 cdp enable
 mpls ip
 mpls label protocol ldp
 no mop enabled
 no mop sysid
!
interface GigabitEthernet2
 description Link to 20-PE
 mtu 8900
 ip address 172.0.2.1 255.255.255.252
 ip ospf authentication message-digest
 ip ospf message-digest-key 1 md5 7 0006121501590A0A03
 ip ospf network point-to-point
 ip ospf dead-interval 15
 ip ospf hello-interval 5
 ip ospf mtu-ignore
 ip ospf cost 1
 load-interval 30
 negotiation auto
 cdp enable
 mpls ip
 mpls label protocol ldp
 no mop enabled
 no mop sysid
!
interface GigabitEthernet3
 mtu 2000
 no ip address
 ip ospf authentication message-digest
 ip ospf message-digest-key 1 md5 7 130716010E0E052627
 ip ospf network point-to-point
 ip ospf dead-interval 15
 ip ospf hello-interval 5
 ip ospf mtu-ignore
 ip ospf cost 65000
 load-interval 30
 shutdown
 negotiation auto
 cdp enable
 mpls ip
 mpls label protocol ldp
 no mop enabled
 no mop sysid
!


MPLS LDP Configuration :
---------------
mpls label protocol ldp
mpls ldp neighbor 10.10.10.10 password baseball
mpls ldp neighbor 20.20.20.20 password baseball
mpls ldp graceful-restart
no mpls ldp advertise-labels
mpls ldp advertise-labels for ACL-MPLS-LDP


Access Control List For LDP :
---------------
ip access-list standard ACL-MPLS-LDP
10 permit 11.11.11.11
20 permit 10.10.10.10
30 permit 1.1.1.1
40 permit 20.20.20.20
50 permit 21.21.21.21
60 permit 4.4.4.4


Routing OSPF Configuration :
---------------
router ospf 100
router-id 1.1.1.1
nsf cisco
passive-interface default
no passive-interface GigabitEthernet1
no passive-interface GigabitEthernet2
network 1.1.1.1 0.0.0.0 area 0
network 172.0.1.2 0.0.0.0 area 0
network 172.0.2.1 0.0.0.0 area 0


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



 







