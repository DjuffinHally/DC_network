# Homework 6

![](https://github.com/DjuffinHally/DC_network/blob/main/hw6/netscheme.png)

## Адресация
```
              sp-1                sp-2
lo0         10.0.1.0/32       10.0.2.0/32
lo1         10.0.1.1/32       10.0.2.1/32
eth1/1      10.2.1.0/31       10.2.2.0/31
eth1/2      10.2.1.2/31       10.2.2.2/31
eth1/3      10.2.1.4/31       10.2.2.4/31

          lf-1             lf-2           lf-3  
lo0     10.1.1.0/32     10.1.2.0/32     10.1.3.0/32
lo1     10.1.1.1/32     10.1.2.1/32     10.1.3.1/32
eth1/1  10.2.1.1/31     10.2.1.3/31     10.2.1.5/31
eth1/2  10.2.2.1/31     10.2.2.3/31     10.2.2.5/31
eth1/6  Vlan10          Vlan20          Vlan10
eth1/7                                  Vlan20
Vlan10  10.4.1.1/24                     10.4.1.1/24
Vlan20                  10.4.2.1/24     10.4.2.1/24


```

## Конфигурация


### spine_1
```
router bgp 65000
  router-id 10.0.1.0
  reconnect-interval 12
  address-family ipv4 unicast
    redistribute direct route-map REDISTRIBUTE_CONNECTED
  template peer LEAF-PEER-TEMPLATE
    description leaf_template
    timers 3 9
    address-family ipv4 unicast
    address-family l2vpn evpn
      send-community
      send-community extended
      route-reflector-client
  neighbor 10.2.1.1
    inherit peer LEAF-PEER-TEMPLATE
    remote-as 65001
    description leaf_1
  neighbor 10.2.1.3
    inherit peer LEAF-PEER-TEMPLATE
    remote-as 65002
    description leaf_2
  neighbor 10.2.1.5
    inherit peer LEAF-PEER-TEMPLATE
    remote-as 65003
    description leaf_3
```

### spine_2
```
router bgp 65000
  router-id 10.0.1.0
  reconnect-interval 12
  address-family ipv4 unicast
    redistribute direct route-map REDISTRIBUTE_CONNECTED
  template peer LEAF-PEER-TEMPLATE
    description leaf_template
    timers 3 9
    address-family ipv4 unicast
    address-family l2vpn evpn
      send-community
      send-community extended
      route-reflector-client
  neighbor 10.2.1.1
    inherit peer LEAF-PEER-TEMPLATE
    remote-as 65001
    description leaf_1
  neighbor 10.2.1.3
    inherit peer LEAF-PEER-TEMPLATE
    remote-as 65002
    description leaf_2
  neighbor 10.2.1.5
    inherit peer LEAF-PEER-TEMPLATE
    remote-as 65003
    description leaf_3
```

### leaf_1
```
router bgp 65001
  router-id 10.1.1.0
  bestpath as-path multipath-relax
  reconnect-interval 12
  address-family ipv4 unicast
    redistribute direct route-map REDISTRIBUTE_CONNECTED
  template peer SPINE-PEER-TEMPLATE
    remote-as 65000
    description leaf_template
    timers 3 9
    address-family ipv4 unicast
    address-family l2vpn evpn
      send-community
      send-community extended
  neighbor 10.2.1.0
    inherit peer SPINE-PEER-TEMPLATE
    description spine_1
  neighbor 10.2.2.0
    inherit peer SPINE-PEER-TEMPLATE
    description spine_2
evpn
  vni 10010 l2
    rd 10010:1
    route-target import 10010:1
    route-target export 10010:1

interface nve1
  no shutdown
  host-reachability protocol bgp
  source-interface loopback1
  member vni 10010
    suppress-arp
    ingress-replication protocol bgp
```

### leaf_2
```
router bgp 65002
  router-id 10.1.2.0
  bestpath as-path multipath-relax
  reconnect-interval 12
  address-family ipv4 unicast
    redistribute direct route-map REDISTRIBUTE_CONNECTED
  template peer SPINE-PEER-TEMPLATE
    remote-as 65000
    description leaf_template
    timers 3 9
    address-family ipv4 unicast
    address-family l2vpn evpn
      send-community
      send-community extended
  neighbor 10.2.1.2
    inherit peer SPINE-PEER-TEMPLATE
    description spine_1
  neighbor 10.2.2.2
    inherit peer SPINE-PEER-TEMPLATE
    description spine_2
evpn
  vni 10020 l2
    rd 10020:1
    route-target import 10020:1
    route-target export 10020:1

interface nve1
  no shutdown
  host-reachability protocol bgp
  source-interface loopback1
  member vni 10020
    suppress-arp
    ingress-replication protocol bgp
```

### leaf_3
```
router bgp 65003
  router-id 10.1.3.0
  bestpath as-path multipath-relax
  reconnect-interval 12
  address-family ipv4 unicast
    redistribute direct route-map REDISTRIBUTE_CONNECTED
  template peer SPINE-PEER-TEMPLATE
    remote-as 65000
    description leaf_template
    timers 3 9
    address-family ipv4 unicast
    address-family l2vpn evpn
      send-community
      send-community extended
  neighbor 10.2.1.4
    inherit peer SPINE-PEER-TEMPLATE
    description spine_1
  neighbor 10.2.2.4
    inherit peer SPINE-PEER-TEMPLATE
    description spine_2
evpn
  vni 10010 l2
    rd 10010:1
    route-target import 10010:1
    route-target export 10010:1
  vni 10020 l2
    rd 10020:1
    route-target import 10020:1
    route-target export 10020:1

interface nve1
  no shutdown
  host-reachability protocol bgp
  source-interface loopback1
  member vni 10010
    suppress-arp
    ingress-replication protocol bgp
  member vni 10020
    suppress-arp
    ingress-replication protocol bgp
```

## Проверка работоспособности

```
leaf_3# sh bgp l2vpn evpn summary
BGP summary information for VRF default, address family L2VPN EVPN
BGP router identifier 10.1.3.0, local AS number 65003
BGP table version is 141, L2VPN EVPN config peers 2, capable peers 2
4 network entries and 6 paths using 1200 bytes of memory
BGP attribute entries [4/672], BGP AS path entries [2/20]
BGP community entries [0/0], BGP clusterlist entries [0/0]

Neighbor        V    AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.2.1.4        4 65000    9363    9358      141    0    0 02:22:21 2
10.2.2.4        4 65000    9334    9296      141    0    0 00:16:49 2

leaf_3# sh bgp l2vpn evpn
BGP routing table information for VRF default, address family L2VPN EVPN
BGP table version is 147, Local Router ID is 10.1.3.0
Status: s-suppressed, x-deleted, S-stale, d-dampened, h-history, *-valid, >-best
Path type: i-internal, e-external, c-confed, l-local, a-aggregate, r-redist, I-i
njected
Origin codes: i - IGP, e - EGP, ? - incomplete, | - multipath, & - backup, 2 - b
est2

   Network            Next Hop            Metric     LocPrf     Weight Path
Route Distinguisher: 10010:1    (L2VNI 10010)
* e[2]:[0]:[0]:[48]:[0050.7966.6803]:[0]:[0.0.0.0]/216
                      10.2.1.4                                       0 65000 650
01 i
*>e                   10.2.2.4                                       0 65000 650
01 i
*>l[2]:[0]:[0]:[48]:[0050.7966.6808]:[0]:[0.0.0.0]/216
                      10.1.3.1                          100      32768 i
* e[2]:[0]:[0]:[48]:[0050.7966.6803]:[32]:[10.4.1.2]/248
                      10.2.1.4                                       0 65000 650
01 i
*>e                   10.2.2.4                                       0 65000 650
01 i
*>l[2]:[0]:[0]:[48]:[0050.7966.6808]:[32]:[10.4.1.3]/248
                      10.1.3.1                          100      32768 i
* e[3]:[0]:[32]:[10.1.1.1]/88
                      10.2.2.4                                       0 65000 650
01 i
*>e                   10.2.1.4                                       0 65000 650
01 i
*>l[3]:[0]:[32]:[10.1.3.1]/88
                      10.1.3.1                          100      32768 i

Route Distinguisher: 10020:1    (L2VNI 10020)
* e[3]:[0]:[32]:[10.1.2.1]/88
                      10.2.2.4                                       0 65000 650
02 i
*>e                   10.2.1.4                                       0 65000 650
02 i
*>l[3]:[0]:[32]:[10.1.3.1]/88
                      10.1.3.1                          100      32768 i
```