```
!
interface GigabitEthernet1/1
 bridge-group 1
 nameif outside1
 security-level 0
!
interface GigabitEthernet1/2
 bridge-group 2
 nameif inside1
 security-level 100
!
interface GigabitEthernet1/3
 bridge-group 2
 nameif inside2
 security-level 100
!
interface GigabitEthernet1/4
 nameif dmz
 security-level 50
 ip address 172.17.18.1 255.255.255.0 
!
interface GigabitEthernet1/5
 bridge-group 1
 nameif outside2
 security-level 0
!
interface GigabitEthernet1/6
 shutdown
 no nameif
 no security-level
 no ip address
!
interface GigabitEthernet1/7
 shutdown
 no nameif
 no security-level
 no ip address
!             
interface GigabitEthernet1/8
 shutdown
 no nameif
 no security-level
 no ip address
!
interface Management1/1
 management-only
 shutdown
 no nameif
 no security-level
 no ip address
!
interface BVI1
 nameif inside
 security-level 100
 ip address 172.18.18.1 255.255.255.0 
!
interface BVI2
 nameif outside
 security-level 0
 ip address 172.16.18.1 255.255.255.0 
!
ftp mode passive
object network HTTP
 host 172.17.18.254
object network dmz
 subnet 172.17.18.0 255.255.255.0
object network HTTPS
 host 172.17.18.254
object network dmz1
 subnet 172.17.18.0 255.255.255.0
object network inside
 subnet 172.16.0.0 255.240.0.0
object network inside1
 subnet 172.16.0.0 255.240.0.0
access-list INTERNET extended permit icmp any any echo-reply 
access-list INTERNET extended permit udp any eq domain any 
access-list INTERNET extended permit tcp any eq www any 
access-list INTERNET extended permit tcp any eq https any 
access-list INTERNET extended permit tcp any any eq www 
access-list INTERNET extended permit tcp any any eq https 
access-list INTERNET extended permit icmp any any 
access-list DMZ extended permit ip any 172.17.18.0 255.255.255.0 
access-list DMZ extended permit tcp any eq www any 
access-list DMZ extended permit tcp any any eq www 
access-list DMZ extended permit udp any any eq domain 
access-list DMZ extended permit icmp any any 
access-list DMZ extended permit tcp any any eq https 
access-list DMZ extended permit tcp any eq https any 
access-list OUTSIDE extended permit icmp any any 
access-list OUTSIDE extended permit ip any any 
pager lines 24
mtu outside1 1500
mtu outside2 1500
mtu dmz 1500
mtu inside2 1500
mtu inside1 1500
icmp unreachable rate-limit 1 burst-size 1
no asdm history enable
arp timeout 14400
no arp permit-nonconnected
arp rate-limit 16384
!
object network HTTP
 nat (dmz,outside1) static 1.18.0.2
object network dmz
 nat (dmz,outside1) dynamic interface
object network HTTPS
 nat (dmz,outside2) static 1.18.0.2
object network dmz1
 nat (dmz,outside2) dynamic interface
object network inside
 nat (inside1,outside1) dynamic interface
object network inside1
 nat (inside2,outside1) dynamic interface
access-group DMZ in interface dmz
access-group OUTSIDE out interface inside
access-group INTERNET in interface outside
timeout xlate 3:00:00
timeout pat-xlate 0:00:30
timeout conn 1:00:00 half-closed 0:10:00 udp 0:02:00 sctp 0:02:00 icmp 0:00:02
timeout sunrpc 0:10:00 h323 0:05:00 h225 1:00:00 mgcp 0:05:00 mgcp-pat 0:05:00
timeout sip 0:30:00 sip_media 0:02:00 sip-invite 0:03:00 sip-disconnect 0:02:00
timeout sip-provisional-media 0:02:00 uauth 0:05:00 absolute
timeout tcp-proxy-reassembly 0:01:00
timeout floating-conn 0:00:00
timeout conn-holddown 0:00:15
timeout igp stale-route 0:01:10
user-identity default-domain LOCAL
aaa authentication login-history
no snmp-server location
no snmp-server contact
service sw-reset-button
crypto ipsec security-association pmtu-aging infinite
crypto ca trustpool policy
telnet timeout 5
ssh stricthostkeycheck
ssh timeout 5
ssh version 2
ssh key-exchange group dh-group1-sha1
console timeout 0

dhcpd dns 8.8.8.8
!
dhcpd address 172.18.18.2-172.18.18.10 inside
dhcpd option 150 ip 172.18.18.1 interface inside
dhcpd enable inside
!
threat-detection basic-threat
threat-detection statistics access-list
no threat-detection statistics tcp-intercept
dynamic-access-policy-record DfltAccessPolicy
!
class-map inspection_default
 match default-inspection-traffic
!
!
policy-map type inspect dns preset_dns_map
 parameters   
  message-length maximum client auto
  message-length maximum 512
  no tcp-inspection
policy-map global_policy
 class inspection_default
  inspect ftp 
  inspect h323 h225 
  inspect h323 ras 
  inspect ip-options 
  inspect netbios 
  inspect rsh 
  inspect rtsp 
  inspect skinny  
  inspect esmtp 
  inspect sqlnet 
  inspect sunrpc 
  inspect tftp 
  inspect sip  
  inspect xdmcp 
  inspect dns preset_dns_map 
policy-map type inspect dns migrated_dns_map_2
 parameters
  message-length maximum client auto
  message-length maximum 512
  no tcp-inspection
policy-map type inspect dns migrated_dns_map_1
 parameters
  message-length maximum client auto
  message-length maximum 512
  no tcp-inspection
!
service-policy global_policy global
prompt hostname context 
call-home reporting anonymous prompt 2
call-home
 profile CiscoTAC-1
  no active
  destination address http https://tools.cisco.com/its/service/oddce/services/DDCEService
  destination address email callhome@cisco.com
  destination transport-method http
  subscribe-to-alert-group diagnostic
  subscribe-to-alert-group environment
  subscribe-to-alert-group inventory periodic monthly
  subscribe-to-alert-group configuration periodic monthly
  subscribe-to-alert-group telemetry periodic daily
Cryptochecksum:283abb71ec510b066c40e86a84c0387c
: end
```
