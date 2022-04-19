
hostname ALFA
!
boot-start-marker
boot-end-marker
!
!
!
no aaa new-model
!
dot11 syslog
ip source-route
!
!
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
!
!
!
!
voice-card 0
!
crypto pki token default removal timeout 0
!
!
!
!
license udi pid CISCO2801 sn FCZ105123FZ
!         
redundancy
!
!
! 
!
crypto isakmp policy 10
 encr aes 256
 authentication pre-share
 group 5
 lifetime 3600
crypto isakmp key Passw0rd address 1.18.0.1
!
!
crypto ipsec transform-set TRANS ah-sha-hmac esp-aes 256 esp-sha-hmac 
!
crypto map OMAPA 10 ipsec-isakmp 
 set peer 1.18.0.1
 set transform-set TRANS 
 match address 100
!
!
!
!         
!
!
interface Tunnel100
 ip address 192.168.18.2 255.255.255.252
 tunnel source Serial0/1/1
 tunnel destination 1.18.0.1
!
interface FastEthernet0/0
 ip address 172.16.18.1 255.255.255.0
 ip nat inside
 ip virtual-reassembly in
 duplex auto
 speed auto
!
interface FastEthernet0/1
 no ip address
 shutdown
 duplex auto
 speed auto
!
interface Serial0/1/0
 no ip address
 shutdown 
 clock rate 125000
!
interface Serial0/1/1
 ip address 1.18.0.2 255.255.255.252
 ip nat outside
 ip virtual-reassembly in
 clock rate 125000
 crypto map OMAPA
!
!
router eigrp 100
 network 172.16.18.0 0.0.0.255
 network 172.17.18.0 0.0.0.255
 network 172.18.18.0 0.0.0.255
 network 192.168.18.0 0.0.0.3
 redistribute static
 passive-interface FastEthernet0/0
!
ip forward-protocol nd
!
!
no ip http server
no ip http secure-server
ip nat inside source list 1 interface Serial0/1/1 overload
ip nat inside source static tcp 172.17.18.2 80 1.18.0.2 80 
ip nat inside source static tcp 172.17.18.2 443 1.18.0.2 443
!
logging esm config
access-list 1 permit 10.0.0.0 0.255.255.255
access-list 1 permit 172.16.0.0 0.15.255.255
access-list 1 permit 192.168.0.0 0.0.255.255
!
access-list 100 permit tcp any any eq www
access-list 100 permit tcp any any eq 443
access-list 100 permit ip any 1.18.0.0 0.0.0.3
access-list 100 permit gre host 1.18.0.2 host 1.18.0.1
!
ip route 172.17.18.0 255.255.255.0 172.16.18.2 
ip route 172.18.18.0 255.255.255.0 172.16.18.2
!
!
!
!
!
!
control-plane
!
!
!
!
!
!
!
!
line con 0
line aux 0
line vty 0 4
 login
 transport input all
!
scheduler allocate 20000 1000
end
