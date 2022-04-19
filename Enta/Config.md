```
hostname ENTA
!
boot-start-marker
boot-end-marker
!
!
!
no aaa new-model
!
!
!
!
!
!
!
!         
!
!
!
ip dhcp excluded-address 10.0.18.1
ip dhcp excluded-address 10.1.18.1
ip dhcp excluded-address 10.2.18.1
!
ip dhcp pool VLAN10
 network 10.0.18.0 255.255.255.0
 default-router 10.0.18.1 
 option 150 ip 10.1.18.1 
 dns-server 8.8.8.8 
!
ip dhcp pool VLAN20
 network 10.1.18.0 255.255.255.0
 default-router 10.1.18.1 
 option 150 ip 10.1.18.1 
 dns-server 8.8.8.8 
!
ip dhcp pool VLAN30
 network 10.2.18.0 255.255.255.0
 default-router 10.2.18.1 
 option 150 ip 10.1.18.1 
 dns-server 8.8.8.8 
!
!
!
ip cef
no ipv6 cef
!
multilink bundle-name authenticated
!
!
!
!
!
!
cts logging verbose
!
!
voice-card 0
!
!
!
!
!         
!
!
!
license udi pid CISCO2911/K9 sn FCZ16337LEM
license boot module c2900 technology-package securityk9
license boot module c2900 technology-package uck9
!
!
!
redundancy
!
!
!
!
!
! 
!
crypto isakmp policy 10
 encr aes 256
 authentication pre-share
 group 5
 lifetime 3600
crypto isakmp key Passw0rd address 1.18.0.2       
crypto isakmp key Passw0rd address 2.18.0.2       
!
!
crypto ipsec transform-set TRANS ah-sha-hmac esp-aes 256 esp-sha-hmac 
 mode tunnel
crypto ipsec transform-set TRANS2 ah-sha-hmac esp-aes 256 esp-sha-hmac 
 mode tunnel
!
!
!
crypto map MAPA 10 ipsec-isakmp 
 set peer 2.18.0.2
 set transform-set TRANS2 
 match address 100
!
crypto map OMAPA 10 ipsec-isakmp 
 set peer 1.18.0.2
 set transform-set TRANS 
 match address 100
!
!
!
!         
!
interface Tunnel100
 ip address 192.168.18.1 255.255.255.252
 tunnel source Serial0/0/0
 tunnel destination 1.18.0.2
!
interface Tunnel200
 ip address 192.168.18.5 255.255.255.252
 tunnel source Serial0/0/1
 tunnel destination 2.18.0.2
!
interface Embedded-Service-Engine0/0
 no ip address
 shutdown
!
interface GigabitEthernet0/0
 no ip address
 duplex auto
 speed auto
!
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 10.0.18.1 255.255.255.0
 ip nat inside
 ip virtual-reassembly in
!
interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 10.1.18.1 255.255.255.0
 ip nat inside
 ip virtual-reassembly in
!
interface GigabitEthernet0/0.30
 encapsulation dot1Q 30
 ip address 10.2.18.1 255.255.255.0
 ip nat inside
 ip virtual-reassembly in
!
interface GigabitEthernet0/1
 no ip address
 duplex auto
 speed auto
!
interface GigabitEthernet0/2
 no ip address
 shutdown 
 duplex auto
 speed auto
!
interface Serial0/0/0
 ip address 1.18.0.1 255.255.255.252
 ip nat outside
 ip virtual-reassembly in
 crypto map OMAPA
!
interface Serial0/0/1
 ip address 2.18.0.1 255.255.255.252
 ip nat outside
 clock rate 2000000
 crypto map MAPA
!
!
router eigrp 100
 no auto-summary
 redistribute static
 network 10.0.18.0 0.0.0.255
 network 10.1.18.0 0.0.0.255
 network 10.2.18.0 0.0.0.255
 network 192.168.18.0 0.0.0.3
 passive-interface GigabitEthernet0/0
 passive-interface GigabitEthernet0/1
!
!
router eigrp 200
 no auto-summary
 redistribute static
 network 10.0.18.0 0.0.0.255
 network 10.1.18.0 0.0.0.255
 network 10.2.18.0 0.0.0.255
 network 192.168.18.4 0.0.0.3
 passive-interface GigabitEthernet0/0
 passive-interface GigabitEthernet0/1
!
ip forward-protocol nd
!
no ip http server
no ip http secure-server
!
ip nat inside source list 1 interface Serial0/0/0 overload
ip nat inside source list 2 interface Serial0/0/1 overload
!
!
!
access-list 1 permit 10.0.0.0 0.255.255.255
access-list 1 permit 172.16.0.0 0.15.255.255
access-list 1 permit 192.168.0.0 0.0.255.255
access-list 2 permit 10.0.0.0 0.255.255.255
access-list 2 permit 172.16.0.0 0.15.255.255
access-list 2 permit 192.168.0.0 0.0.255.255

access-list 100 permit gre host 1.18.0.1 host 1.18.0.2
access-list 101 permit gre host 2.18.0.1 host 2.18.0.2
!
control-plane
!
 !
 !
 !
 !
!
mgcp behavior rsip-range tgcp-only
mgcp behavior comedia-role none
mgcp behavior comedia-check-media-src disable
mgcp behavior comedia-sdp-force disable
!
mgcp profile default
!
!
!
!
!
!         
!
gatekeeper
 shutdown
!
!
telephony-service
 max-ephones 10
 max-dn 10
 ip source-address 10.1.18.1 port 2000
 auto assign 1 to 10
 create cnf-files
!
!
ephone-dn  1
 number 100
!
!
ephone-dn  2
 number 101
!
!
ephone-dn  3
 number 102
!
!
!
line con 0
line aux 0
line 2
 no activation-character
 no exec
 transport preferred none
 transport output pad telnet rlogin lapb-ta mop udptn v120 ssh
 stopbits 1
line vty 0 4
 login
 transport input none
!
scheduler allocate 20000 1000
!
end
```
