hostname Switch11
!
boot-start-marker
boot-end-marker
!
!
!
!
no aaa new-model
system mtu routing 1500
!
!
!
!
!         
!
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
spanning-tree vlan 30 priority 28672
!
vlan internal allocation policy ascending
!
!
!
interface Port-channel2
 switchport mode trunk
!
interface FastEthernet0/1
!
interface FastEthernet0/2
!
interface FastEthernet0/3
!
interface FastEthernet0/4
 switchport access vlan 30
 switchport mode access
!
interface FastEthernet0/5
 switchport mode access
 switchport voice vlan 20
 mls qos trust cos
 spanning-tree portfast
!
interface FastEthernet0/6
!
interface FastEthernet0/7
!
interface FastEthernet0/8
!
interface FastEthernet0/9
!
interface FastEthernet0/10
!
interface FastEthernet0/11
 switchport mode trunk
 channel-protocol lacp
 channel-group 2 mode active
!         
interface FastEthernet0/12
 switchport mode trunk
 channel-protocol lacp
 channel-group 2 mode active
!
interface FastEthernet0/13
 switchport mode trunk
!
interface FastEthernet0/14
!
interface FastEthernet0/15
!
interface FastEthernet0/16
!
interface FastEthernet0/17
!
interface FastEthernet0/18
!
interface FastEthernet0/19
!
interface FastEthernet0/20
!
interface FastEthernet0/21
!
interface FastEthernet0/22
!
interface FastEthernet0/23
!
interface FastEthernet0/24
!
interface GigabitEthernet0/1
!
interface GigabitEthernet0/2
!
interface Vlan1
 no ip address
 shutdown
!
ip http server
ip http secure-server
ip sla enable reaction-alerts
!
line con 0
line vty 5 15
!
end
