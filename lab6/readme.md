# Домашние задание №6
## EVPN L3VNI

### Схема сети

![scheme.png](scheme.png)

## Конфигурация и таблица маршрутизации

<details>
  <summary><b> Spine-1 </b></summary>
  <p> 

```
nv overlay evpn
feature bgp

route-map leaf-ases permit 10
 match as-number 65301-65399 
route-map nhu permit 10
  set ip next-hop unchanged
route-map redis permit 10
  match interface loopback0 loopback1

interface Ethernet1/1
 no switchport
 mtu 9216
 port-type fabric
 no ip redirects
 ip address 10.20.1.0/31
 no shutdown

interface Ethernet1/2
 no switchport
 mtu 9216
 port-type fabric
 no ip redirects
 ip address 10.20.1.2/31
 no shutdown

interface Ethernet1/3
 no switchport
 mtu 9216
 port-type fabric
 no ip redirects
 ip address 10.20.1.4/31
 no shutdown

interface loopback0
 ip address 10.10.1.0/32

interface loopback1
 ip address 10.11.1.0/32

router bgp 65000
 router-id 10.10.1.0
 timers bgp 3 9
 reconnect-interval 12
 log-neighbor-changes
 address-family ipv4 unicast
  redistribute direct route-map redis
  maximum-paths 10
 address-family l2vpn evpn
  maximum-paths 10
  retain route-target all
 neighbor 10.10.0.0/24 remote-as route-map leaf-ases
  remote-as external
  update-source loopback0
  ebgp-multihop 5
  address-family l2vpn evpn
    send-community
    send-community extended
    route-map nhu out
    rewrite-evpn-rt-asn
 neighbor 10.20.1.0/24 remote-as route-map leaf-ases
  address-family ipv4 unicast
```
### Вывод маршрутной информации
```
10.10.0.1/32, ubest/mbest: 1/0
    *via 10.20.1.1, [20/0], 00:37:19, bgp-65000, external, tag 65301
10.10.0.2/32, ubest/mbest: 1/0
    *via 10.20.1.3, [20/0], 00:30:33, bgp-65000, external, tag 65302
10.10.0.3/32, ubest/mbest: 1/0
    *via 10.20.1.5, [20/0], 00:33:57, bgp-65000, external, tag 65303
10.10.1.0/32, ubest/mbest: 2/0, attached
    *via 10.10.1.0, Lo0, [0/0], 00:41:14, local
    *via 10.10.1.0, Lo0, [0/0], 00:41:14, direct
10.11.0.1/32, ubest/mbest: 1/0
    *via 10.20.1.1, [20/0], 00:37:19, bgp-65000, external, tag 65301
10.11.0.2/32, ubest/mbest: 1/0
    *via 10.20.1.3, [20/0], 00:30:33, bgp-65000, external, tag 65302
10.11.0.3/32, ubest/mbest: 1/0
    *via 10.20.1.5, [20/0], 00:33:57, bgp-65000, external, tag 65303
10.11.1.0/32, ubest/mbest: 2/0, attached
    *via 10.11.1.0, Lo1, [0/0], 00:41:13, local
    *via 10.11.1.0, Lo1, [0/0], 00:41:13, direct
10.20.1.0/31, ubest/mbest: 1/0, attached
    *via 10.20.1.0, Eth1/1, [0/0], 00:41:41, direct
10.20.1.0/32, ubest/mbest: 1/0, attached
    *via 10.20.1.0, Eth1/1, [0/0], 00:41:41, local
10.20.1.2/31, ubest/mbest: 1/0, attached
    *via 10.20.1.2, Eth1/2, [0/0], 00:41:40, direct
10.20.1.2/32, ubest/mbest: 1/0, attached
    *via 10.20.1.2, Eth1/2, [0/0], 00:41:40, local
10.20.1.4/31, ubest/mbest: 1/0, attached
    *via 10.20.1.4, Eth1/3, [0/0], 00:41:40, direct
10.20.1.4/32, ubest/mbest: 1/0, attached
    *via 10.20.1.4, Eth1/3, [0/0], 00:41:40, local
```
### Соседи spine1
```
Neighbor        V    AS    MsgRcvd    MsgSent   TblVer  InQ OutQ Up/Down  State/
PfxRcd
10.20.1.1       4 65301        903        896       34    0    0 00:37:41 2     
    
10.20.1.3       4 65302        733        731       34    0    0 00:30:56 2     
    
10.20.1.5       4 65303       1028       1024       34    0    0 00:34:19 2 
```
</p>
</details>

<details>
  <summary><b> Spine-2 </b></summary>
  <p> 

```
nv overlay evpn
feature bgp

route-map leaf-ases permit 10
 match as-number 65301-65399 
route-map nhu permit 10
  set ip next-hop unchanged
route-map redis permit 10
  match interface loopback0 loopback1

interface Ethernet1/1
 no switchport
 mtu 9216
 port-type fabric
 no ip redirects
 ip address 10.20.2.0/31
 no shutdown

interface Ethernet1/2
 no switchport
 mtu 9216
 port-type fabric
 no ip redirects
 ip address 10.20.2.2/31
 no shutdown

interface Ethernet1/3
 no switchport
 mtu 9216
 port-type fabric
 no ip redirects
 ip address 10.20.2.4/31
 no shutdown

interface loopback0
 ip address 10.10.2.0/32

interface loopback1
 ip address 10.11.2.0/32

router bgp 65000
 router-id 10.10.2.0
 timers bgp 3 9
 reconnect-interval 12
 log-neighbor-changes
 address-family ipv4 unicast
  redistribute direct route-map redis
  maximum-paths 10
 address-family l2vpn evpn
  maximum-paths 10
  retain route-target all
 neighbor 10.10.0.0/24 remote-as route-map leaf-ases
  remote-as external
  update-source loopback0
  ebgp-multihop 5
  address-family l2vpn evpn
    send-community
    send-community extended
    route-map nhu out
    rewrite-evpn-rt-asn
 neighbor 10.20.2.0/24 remote-as route-map leaf-ases
  address-family ipv4 unicast
```
### Вывод маршрутной информации
```
10.10.0.1/32, ubest/mbest: 1/0
    *via 10.20.2.1, [20/0], 00:38:09, bgp-65000, external, tag 65301
10.10.0.2/32, ubest/mbest: 1/0
    *via 10.20.2.3, [20/0], 00:31:23, bgp-65000, external, tag 65302
10.10.0.3/32, ubest/mbest: 1/0
    *via 10.20.2.5, [20/0], 00:34:47, bgp-65000, external, tag 65303
10.10.2.0/32, ubest/mbest: 2/0, attached
    *via 10.10.2.0, Lo0, [0/0], 00:40:31, local
    *via 10.10.2.0, Lo0, [0/0], 00:40:31, direct
10.11.0.1/32, ubest/mbest: 1/0
    *via 10.20.2.1, [20/0], 00:38:09, bgp-65000, external, tag 65301
10.11.0.2/32, ubest/mbest: 1/0
    *via 10.20.2.3, [20/0], 00:31:23, bgp-65000, external, tag 65302
10.11.0.3/32, ubest/mbest: 1/0
    *via 10.20.2.5, [20/0], 00:34:47, bgp-65000, external, tag 65303
10.11.2.0/32, ubest/mbest: 2/0, attached
    *via 10.11.2.0, Lo1, [0/0], 00:40:31, local
    *via 10.11.2.0, Lo1, [0/0], 00:40:31, direct
10.20.2.0/31, ubest/mbest: 1/0, attached
    *via 10.20.2.0, Eth1/1, [0/0], 00:40:32, direct
10.20.2.0/32, ubest/mbest: 1/0, attached
    *via 10.20.2.0, Eth1/1, [0/0], 00:40:32, local
10.20.2.2/31, ubest/mbest: 1/0, attached
    *via 10.20.2.2, Eth1/2, [0/0], 00:40:31, direct
10.20.2.2/32, ubest/mbest: 1/0, attached
    *via 10.20.2.2, Eth1/2, [0/0], 00:40:31, local
10.20.2.4/31, ubest/mbest: 1/0, attached
    *via 10.20.2.4, Eth1/3, [0/0], 00:40:31, direct
10.20.2.4/32, ubest/mbest: 1/0, attached
    *via 10.20.2.4, Eth1/3, [0/0], 00:40:31, local
```
### Соседи spine2
```
Neighbor        V    AS    MsgRcvd    MsgSent   TblVer  InQ OutQ Up/Down  State/
PfxRcd
10.20.2.1       4 65301        848        842       34    0    0 00:37:52 2     
    
10.20.2.3       4 65302        737        735       34    0    0 00:31:05 2     
    
10.20.2.5       4 65303        962        957       34    0    0 00:34:30 2      
```
</p>
</details>

<details>
  <summary><b> Leaf-1</b></summary>
  <p>
 
```
nv overlay evpn
feature bgp
feature pim
feature fabric forwarding
feature interface-vlan
feature vn-segment-vlan-based
feature lacp
feature nv overlay


hardware access-list tcam region racl 512
hardware access-list tcam region arp-ether 256 double-wide

fabric forwarding anycast-gateway-mac 0000.0000.0001
vlan 1-2,10,20
vlan 2
  name VXLAN-L3
  vn-segment 5000
vlan 10
  name vlan10
  vn-segment 10010
vlan 20
  name vlan20
  vn-segment 10020
  
route-map redis permit 10
  match interface loopback0 loopback1

vrf context VXLAN
  vni 5000
  rd auto
  address-family ipv4 unicast
    route-target both auto
    route-target both auto evpn
vrf context management
  
interface Vlan1

interface Vlan2
  description VXLAN-L3
  no shutdown
  vrf member VXLAN
  ip forward

interface Vlan10
  no shutdown
  vrf member VXLAN
  ip address 192.168.10.1/24
  fabric forwarding mode anycast-gateway

interface Vlan20
  no shutdown
  vrf member VXLAN
  ip address 192.168.20.1/24
  fabric forwarding mode anycast-gateway

interface nve1
  no shutdown
  host-reachability protocol bgp
  source-interface loopback0
  global suppress-arp
  global ingress-replication protocol bgp
  member vni 5000 associate-vrf
  member vni 10010
  member vni 10020

interface Ethernet1/1
 no switchport
 mtu 9216
 port-type fabric
 no ip redirects
 ip address 10.20.1.1/31
 no shutdown

interface Ethernet1/2
 no switchport
 mtu 9216
 port-type fabric
 no ip redirects
 ip address 10.20.2.1/31
 no shutdown
 
interface Ethernet1/6
  switchport access vlan 10

interface Ethernet1/7
  switchport access vlan 10

interface loopback0
 ip address 10.10.0.1/32
 
interface loopback1
 ip address 10.11.0.1/32

router bgp 65301
 router-id 10.10.0.1
 bestpath as-path multipath-relax
 address-family ipv4 unicast
  redistribute direct route-map redis
  maximum-paths 10
 address-family l2vpn evpn
  maximum-paths 10
 template peer forspines
  remote-as 65000
  timers 3 9
  address-family ipv4 unicast
 template peer spinesover
  remote-as 65000
  update-source loopback0
  ebgp-multihop 2
  timers 3 9
   address-family l2vpn evpn
     send-community
     send-community extended
     rewrite-evpn-rt-asn
  neighbor 10.10.1.0
    inherit peer spinesover
  neighbor 10.10.2.0
    inherit peer spinesover
  neighbor 10.20.1.0
    inherit peer forspines
  neighbor 10.20.2.0
    inherit peer forspines
evpn
  vni 10010 l2
    rd auto
    route-target import auto
    route-target export auto
```
### Вывод маршрутной информации
```
10.10.0.1/32, ubest/mbest: 2/0, attached
    *via 10.10.0.1, Lo0, [0/0], 00:39:07, local
    *via 10.10.0.1, Lo0, [0/0], 00:39:07, direct
10.10.0.2/32, ubest/mbest: 2/0
    *via 10.20.1.0, [20/0], 00:31:50, bgp-65301, external, tag 65000
    *via 10.20.2.0, [20/0], 00:31:50, bgp-65301, external, tag 65000
10.10.0.3/32, ubest/mbest: 2/0
    *via 10.20.1.0, [20/0], 00:35:14, bgp-65301, external, tag 65000
    *via 10.20.2.0, [20/0], 00:35:14, bgp-65301, external, tag 65000
10.10.1.0/32, ubest/mbest: 1/0
    *via 10.20.1.0, [20/0], 00:38:36, bgp-65301, external, tag 65000
10.10.2.0/32, ubest/mbest: 1/0
    *via 10.20.2.0, [20/0], 00:38:37, bgp-65301, external, tag 65000
10.11.0.1/32, ubest/mbest: 2/0, attached
    *via 10.11.0.1, Lo1, [0/0], 00:39:07, local
    *via 10.11.0.1, Lo1, [0/0], 00:39:07, direct
10.11.0.2/32, ubest/mbest: 2/0
    *via 10.20.1.0, [20/0], 00:31:50, bgp-65301, external, tag 65000
    *via 10.20.2.0, [20/0], 00:31:50, bgp-65301, external, tag 65000
10.11.0.3/32, ubest/mbest: 2/0
    *via 10.20.1.0, [20/0], 00:35:14, bgp-65301, external, tag 65000
    *via 10.20.2.0, [20/0], 00:35:14, bgp-65301, external, tag 65000
10.11.1.0/32, ubest/mbest: 1/0
    *via 10.20.1.0, [20/0], 00:38:36, bgp-65301, external, tag 65000
10.11.2.0/32, ubest/mbest: 1/0
    *via 10.20.2.0, [20/0], 00:38:37, bgp-65301, external, tag 65000
10.20.1.0/31, ubest/mbest: 1/0, attached
    *via 10.20.1.1, Eth1/1, [0/0], 3w5d, direct
10.20.1.1/32, ubest/mbest: 1/0, attached
    *via 10.20.1.1, Eth1/1, [0/0], 3w5d, local
10.20.2.0/31, ubest/mbest: 1/0, attached
    *via 10.20.2.1, Eth1/2, [0/0], 3w5d, direct
10.20.2.1/32, ubest/mbest: 1/0, attached
    *via 10.20.2.1, Eth1/2, [0/0], 3w5d, local
```  
### Соседи leaf1
```
Neighbor        V    AS    MsgRcvd    MsgSent   TblVer  InQ OutQ Up/Down  State/
PfxRcd
10.20.1.0       4 65000        787        779       30    0    0 00:38:45 6     
    
10.20.2.0       4 65000        787        779       30    0    0 00:38:46 6 
```
  </p>
</details>

<details>
  <summary><b> Leaf-2</b></summary>
  <p>
 
```
nv overlay evpn
feature bgp
feature pim
feature fabric forwarding
feature interface-vlan
feature vn-segment-vlan-based
feature lacp
feature nv overlay


hardware access-list tcam region racl 512
hardware access-list tcam region arp-ether 256 double-wide

fabric forwarding anycast-gateway-mac 0000.0000.0001
vlan 1-2,10,20
vlan 2
  name VXLAN-L3
  vn-segment 5000
vlan 10
  name vlan10
  vn-segment 10010
vlan 20
  name vlan20
  vn-segment 10020
  
route-map redis permit 10
  match interface loopback0 loopback1

vrf context VXLAN
  vni 5000
  rd auto
  address-family ipv4 unicast
    route-target both auto
    route-target both auto evpn
vrf context management
  
interface Vlan1

interface Vlan2
  description VXLAN-L3
  no shutdown
  vrf member VXLAN
  ip forward

interface Vlan10
  no shutdown
  vrf member VXLAN
  ip address 192.168.10.1/24
  fabric forwarding mode anycast-gateway

interface Vlan20
  no shutdown
  vrf member VXLAN
  ip address 192.168.20.1/24
  fabric forwarding mode anycast-gateway

interface nve1
  no shutdown
  host-reachability protocol bgp
  source-interface loopback0
  global suppress-arp
  global ingress-replication protocol bgp
  member vni 5000 associate-vrf
  member vni 10010
  member vni 10020

interface Ethernet1/1
 no switchport
 mtu 9216
 port-type fabric
 no ip redirects
 ip address 10.20.1.3/31
 no shutdown

interface Ethernet1/2
 no switchport
 mtu 9216
 port-type fabric
 no ip redirects
 ip address 10.20.2.3/31
 no shutdown
 
interface Ethernet1/6
  switchport access vlan 10

interface Ethernet1/7
  switchport access vlan 10

interface loopback0
 ip address 10.10.0.2/32
 
interface loopback1
 ip address 10.11.0.2/32

router bgp 65302
 router-id 10.10.0.2
 bestpath as-path multipath-relax
 address-family ipv4 unicast
  redistribute direct route-map redis
  maximum-paths 10
 address-family l2vpn evpn
  maximum-paths 10
 template peer forspines
  remote-as 65000
  timers 3 9
  address-family ipv4 unicast
 template peer spinesover
  remote-as 65000
  update-source loopback0
  ebgp-multihop 2
  timers 3 9
   address-family l2vpn evpn
     send-community
     send-community extended
     rewrite-evpn-rt-asn
  neighbor 10.10.1.0
    inherit peer spinesover
  neighbor 10.10.2.0
    inherit peer spinesover
  neighbor 10.20.1.2
    inherit peer forspines
  neighbor 10.20.2.2
    inherit peer forspines
evpn
  vni 10010 l2
    rd auto
    route-target import auto
    route-target export auto
```
### Вывод маршрутной информации
```
10.10.0.1/32, ubest/mbest: 2/0
    *via 10.20.1.2, [20/0], 00:31:52, bgp-65302, external, tag 65000
    *via 10.20.2.2, [20/0], 00:31:51, bgp-65302, external, tag 65000
10.10.0.2/32, ubest/mbest: 2/0, attached
    *via 10.10.0.2, Lo0, [0/0], 00:37:42, local
    *via 10.10.0.2, Lo0, [0/0], 00:37:42, direct
10.10.0.3/32, ubest/mbest: 2/0
    *via 10.20.1.2, [20/0], 00:31:52, bgp-65302, external, tag 65000
    *via 10.20.2.2, [20/0], 00:31:51, bgp-65302, external, tag 65000
10.10.1.0/32, ubest/mbest: 1/0
    *via 10.20.1.2, [20/0], 00:31:52, bgp-65302, external, tag 65000
10.10.2.0/32, ubest/mbest: 1/0
    *via 10.20.2.2, [20/0], 00:31:51, bgp-65302, external, tag 65000
10.11.0.1/32, ubest/mbest: 2/0
    *via 10.20.1.2, [20/0], 00:31:52, bgp-65302, external, tag 65000
    *via 10.20.2.2, [20/0], 00:31:51, bgp-65302, external, tag 65000
10.11.0.2/32, ubest/mbest: 2/0, attached
    *via 10.11.0.2, Lo1, [0/0], 00:37:37, local
    *via 10.11.0.2, Lo1, [0/0], 00:37:37, direct
10.11.0.3/32, ubest/mbest: 2/0
    *via 10.20.1.2, [20/0], 00:31:52, bgp-65302, external, tag 65000
    *via 10.20.2.2, [20/0], 00:31:51, bgp-65302, external, tag 65000
10.11.1.0/32, ubest/mbest: 1/0
    *via 10.20.1.2, [20/0], 00:31:52, bgp-65302, external, tag 65000
10.11.2.0/32, ubest/mbest: 1/0
    *via 10.20.2.2, [20/0], 00:31:51, bgp-65302, external, tag 65000
10.20.1.2/31, ubest/mbest: 1/0, attached
    *via 10.20.1.3, Eth1/1, [0/0], 3w5d, direct
10.20.1.3/32, ubest/mbest: 1/0, attached
    *via 10.20.1.3, Eth1/1, [0/0], 3w5d, local
10.20.2.2/31, ubest/mbest: 1/0, attached
    *via 10.20.2.3, Eth1/2, [0/0], 3w5d, direct
10.20.2.3/32, ubest/mbest: 1/0, attached
    *via 10.20.2.3, Eth1/2, [0/0], 3w5d, local
```  
### Соседи leaf2
```
Neighbor        V    AS    MsgRcvd    MsgSent   TblVer  InQ OutQ Up/Down  State/
PfxRcd
10.20.1.2       4 65000        647        643       21    0    0 00:32:00 6     
    
10.20.2.2       4 65000        647        644       21    0    0 00:31:59 6    
```
  </p>
</details>

<details>
  <summary><b> Leaf-3</b></summary>
  <p>
 
```
nv overlay evpn
feature bgp
feature pim
feature fabric forwarding
feature interface-vlan
feature vn-segment-vlan-based
feature lacp
feature nv overlay


hardware access-list tcam region racl 512
hardware access-list tcam region arp-ether 256 double-wide

fabric forwarding anycast-gateway-mac 0000.0000.0001
vlan 1-2,10,20
vlan 2
  name VXLAN-L3
  vn-segment 5000
vlan 10
  name vlan10
  vn-segment 10010
vlan 20
  name vlan20
  vn-segment 10020
  
route-map redis permit 10
  match interface loopback0 loopback1

vrf context VXLAN
  vni 5000
  rd auto
  address-family ipv4 unicast
    route-target both auto
    route-target both auto evpn
vrf context management
  
interface Vlan1

interface Vlan2
  description VXLAN-L3
  no shutdown
  vrf member VXLAN
  ip forward

interface Vlan10
  no shutdown
  vrf member VXLAN
  ip address 192.168.10.1/24
  fabric forwarding mode anycast-gateway

interface Vlan20
  no shutdown
  vrf member VXLAN
  ip address 192.168.20.1/24
  fabric forwarding mode anycast-gateway

interface nve1
  no shutdown
  host-reachability protocol bgp
  source-interface loopback0
  global suppress-arp
  global ingress-replication protocol bgp
  member vni 5000 associate-vrf
  member vni 10010
  member vni 10020

interface Ethernet1/1
 no switchport
 mtu 9216
 port-type fabric
 no ip redirects
 ip address 10.20.1.5/31
 no shutdown

interface Ethernet1/2
 no switchport
 mtu 9216
 port-type fabric
 no ip redirects
 ip address 10.20.2.5/31
 no shutdown
 
interface Ethernet1/6
  switchport access vlan 10

interface Ethernet1/7
  switchport access vlan 10

interface loopback0
 ip address 10.10.0.3/32
 
interface loopback1
 ip address 10.11.0.3/32

router bgp 65303
 router-id 10.10.0.3
 bestpath as-path multipath-relax
 address-family ipv4 unicast
  redistribute direct route-map redis
  maximum-paths 10
 address-family l2vpn evpn
  maximum-paths 10
 template peer forspines
  remote-as 65000
  timers 3 9
  address-family ipv4 unicast
 template peer spinesover
  remote-as 65000
  update-source loopback0
  ebgp-multihop 2
  timers 3 9
   address-family l2vpn evpn
     send-community
     send-community extended
     rewrite-evpn-rt-asn
  neighbor 10.10.1.0
    inherit peer spinesover
  neighbor 10.10.2.0
    inherit peer spinesover
  neighbor 10.20.1.4
    inherit peer forspines
  neighbor 10.20.2.4
    inherit peer forspines
evpn
  vni 10010 l2
    rd auto
    route-target import auto
    route-target export auto
```
### Вывод маршрутной информации
```
10.10.0.1/32, ubest/mbest: 2/0
    *via 10.20.1.4, [20/0], 00:35:14, bgp-65303, external, tag 65000
    *via 10.20.2.4, [20/0], 00:35:15, bgp-65303, external, tag 65000
10.10.0.2/32, ubest/mbest: 2/0
    *via 10.20.1.4, [20/0], 00:31:51, bgp-65303, external, tag 65000
    *via 10.20.2.4, [20/0], 00:31:51, bgp-65303, external, tag 65000
10.10.0.3/32, ubest/mbest: 2/0, attached
    *via 10.10.0.3, Lo0, [0/0], 00:35:59, local
    *via 10.10.0.3, Lo0, [0/0], 00:35:59, direct
10.10.1.0/32, ubest/mbest: 1/0
    *via 10.20.1.4, [20/0], 00:35:14, bgp-65303, external, tag 65000
10.10.2.0/32, ubest/mbest: 1/0
    *via 10.20.2.4, [20/0], 00:35:15, bgp-65303, external, tag 65000
10.11.0.1/32, ubest/mbest: 2/0
    *via 10.20.1.4, [20/0], 00:35:14, bgp-65303, external, tag 65000
    *via 10.20.2.4, [20/0], 00:35:15, bgp-65303, external, tag 65000
10.11.0.2/32, ubest/mbest: 2/0
    *via 10.20.1.4, [20/0], 00:31:51, bgp-65303, external, tag 65000
    *via 10.20.2.4, [20/0], 00:31:51, bgp-65303, external, tag 65000
10.11.0.3/32, ubest/mbest: 2/0, attached
    *via 10.11.0.3, Lo1, [0/0], 00:35:59, local
    *via 10.11.0.3, Lo1, [0/0], 00:35:59, direct
10.11.1.0/32, ubest/mbest: 1/0
    *via 10.20.1.4, [20/0], 00:35:14, bgp-65303, external, tag 65000
10.11.2.0/32, ubest/mbest: 1/0
    *via 10.20.2.4, [20/0], 00:35:15, bgp-65303, external, tag 65000
10.20.1.4/31, ubest/mbest: 1/0, attached
    *via 10.20.1.5, Eth1/1, [0/0], 3w5d, direct
10.20.1.5/32, ubest/mbest: 1/0, attached
    *via 10.20.1.5, Eth1/1, [0/0], 3w5d, local
10.20.2.4/31, ubest/mbest: 1/0, attached
    *via 10.20.2.5, Eth1/2, [0/0], 3w5d, direct
10.20.2.5/32, ubest/mbest: 1/0, attached
    *via 10.20.2.5, Eth1/2, [0/0], 3w5d, local
```  
### Соседи leaf3
```
Neighbor        V    AS    MsgRcvd    MsgSent   TblVer  InQ OutQ Up/Down  State/
PfxRcd
10.20.1.4       4 65000        716        711       27    0    0 00:35:22 6     
    
10.20.2.4       4 65000        715        711       27    0    0 00:35:24 6   
```
  </p>
</details>

### Результат

![icmp.png](icmp.png)
