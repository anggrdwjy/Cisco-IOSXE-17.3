# How To Configuration Switch Cisco vIOS

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

VLAN Configuration :
---------------
VLAN
```
vlan [VLAN_ID]
vlan name [VLAN_NAME]
```
Verification
```
show vlan
```
Interface Configuration :
---------------
Interface VLAN
```
interface vlan [VLAN_ID]
description [SERVICE_NAME]
ip address [IP_ADDRESS] [NETMASK]
no shutdown
```
Port Trunk
```
interface GigabitEthernet [PORT_TRUNK]
description Trunk to 11-CE
switchport trunk allowed vlan 111,444,666
switchport trunk encapsulation dot1q
switchport mode trunk
mtu 1900
load-interval 30
negotiation auto
spanning-tree bpdufilter enable
```
Port Access
```
interface GigabitEthernet [PORT_ACCESS]
description Access to A-CPE
switchport access vlan 444
switchport mode access
mtu 1900
load-interval 30
negotiation auto
spanning-tree bpdufilter enable
```
Verification
```
show interface [PORT_TRUNK] or [PORT_ACCESS]
show running interface [PORT_TRUNK]
show running interface [PORT_ACCESS]
show mac-address-table dynamic vlan [VLAN_ID]
```

Static Routing :
---------------
Static Routing
```
ip route 0.0.0.0 0.0.0.0 [GATEWAY]
ip route [NETWORK_NEIGHBOR] [PREFIX] [GATEWAY]
```
Verification
```
show route table
ping [IP_GATEWAY]
```




