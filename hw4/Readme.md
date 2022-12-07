# Homework 2

![](https://github.com/DjuffinHally/DC_network/blob/main/hw4/netscheme.png)

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
eth1/6  10.4.0.1/24     10.4.1.1/24     10.4.2.1/24
eth1/4                                  10.4.3.1/24
```

## Конфигурация


### spine_1
```
feature bgp
router bgp 65000
  router-id 10.0.1.0
  reconnect-interval 12
  address-family ipv4 unicast
  template peer LEAF-PEER-TEMPLATE
    description leaf_template
    timers 3 9
    address-family ipv4 unicast
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
feature bgp

router bgp 65000
  router-id 10.0.2.0
  reconnect-interval 12
  address-family ipv4 unicast
  template peer LEAF-PEER-TEMPLATE
    description leaf_template
    timers 3 9
    address-family ipv4 unicast
  neighbor 10.2.2.1
    inherit peer LEAF-PEER-TEMPLATE
    remote-as 65001
    description leaf_1
  neighbor 10.2.2.3
    inherit peer LEAF-PEER-TEMPLATE
    remote-as 65002
    description leaf_2
  neighbor 10.2.2.5
    inherit peer LEAF-PEER-TEMPLATE
    remote-as 65003
    description leaf_3
```

### leaf_1
```
feature bgp

route-map REDISTRIBUTE_CONNECTED permit 10
  match interface loopback1

router bgp 65001
  router-id 10.1.1.0
  reconnect-interval 12
  bestpath as-path multipath-relax
  address-family ipv4 unicast
    redistribute direct route-map REDISTRIBUTE_CONNECTED
  template peer SPINE-PEER-TEMPLATE
    description leaf_template
    timers 3 9
    address-family ipv4 unicast
      remote-as 65000
  neighbor 10.2.1.0
    inherit peer SPINE-PEER-TEMPLATE
    description spine_1
  neighbor 10.2.2.0
    inherit peer SPINE-PEER-TEMPLATE
    description spine_2
```

### leaf_2
```
feature bgp

route-map REDISTRIBUTE_CONNECTED permit 10
  match interface loopback1

router bgp 65002
  router-id 10.1.2.0
  reconnect-interval 12
  bestpath as-path multipath-relax
  address-family ipv4 unicast
    redistribute direct route-map REDISTRIBUTE_CONNECTED
  template peer SPINE-PEER-TEMPLATE
    description leaf_template
    timers 3 9
    address-family ipv4 unicast
      remote-as 65000
  neighbor 10.2.1.2
    inherit peer SPINE-PEER-TEMPLATE
    description spine_1
  neighbor 10.2.2.2
    inherit peer SPINE-PEER-TEMPLATE
    description spine_2
```

### leaf_3
```
feature bgp

route-map REDISTRIBUTE_CONNECTED permit 10
  match interface loopback1

router bgp 65003
  router-id 10.1.3.0
  reconnect-interval 12
  bestpath as-path multipath-relax
  address-family ipv4 unicast
    redistribute direct route-map REDISTRIBUTE_CONNECTED
  template peer SPINE-PEER-TEMPLATE
    description leaf_template
    timers 3 9
    address-family ipv4 unicast
      remote-as 65000
  neighbor 10.2.1.4
    inherit peer SPINE-PEER-TEMPLATE
    description spine_1
  neighbor 10.2.2.4
    inherit peer SPINE-PEER-TEMPLATE
    description spine_2
```

## Проверка работоспособности

```
leaf_3(config)# sh ip bgp
BGP routing table information for VRF default, address family IPv4 Unicast
BGP table version is 24, Local Router ID is 10.1.3.0
Status: s-suppressed, x-deleted, S-stale, d-dampened, h-history, *-valid, >-best
Path type: i-internal, e-external, c-confed, l-local, a-aggregate, r-redist, I-i
njected
Origin codes: i - IGP, e - EGP, ? - incomplete, | - multipath, & - backup, 2 - b
est2

   Network            Next Hop            Metric     LocPrf     Weight Path
* e10.1.1.1/32        10.2.2.4                                       0 65000 65001 ?
*>e                   10.2.1.4                                       0 65000 65001 ?
* e10.1.2.1/32        10.2.2.4                                       0 65000 65002 ?
*>e                   10.2.1.4                                       0 65000 65002 ?
*>r10.1.3.1/32        0.0.0.0                  0        100      32768 ?

leaf_3(config)# sh ip route
IP Route Table for VRF "default"
'*' denotes best ucast next-hop
'**' denotes best mcast next-hop
'[x/y]' denotes [preference/metric]
'%<string>' in via output denotes VRF <string>

10.1.1.1/32, ubest/mbest: 1/0
    *via 10.2.1.4, [20/0], 00:00:09, bgp-65003, external, tag 65000
10.1.2.1/32, ubest/mbest: 1/0
    *via 10.2.1.4, [20/0], 00:13:54, bgp-65003, external, tag 65000
10.1.3.0/32, ubest/mbest: 2/0, attached
    *via 10.1.3.0, Lo0, [0/0], 01:19:45, local
    *via 10.1.3.0, Lo0, [0/0], 01:19:45, direct
10.1.3.1/32, ubest/mbest: 2/0, attached
    *via 10.1.3.1, Lo1, [0/0], 01:19:17, local
    *via 10.1.3.1, Lo1, [0/0], 01:19:17, direct
10.2.1.4/31, ubest/mbest: 1/0, attached
    *via 10.2.1.5, Eth1/1, [0/0], 01:19:48, direct
10.2.1.5/32, ubest/mbest: 1/0, attached
    *via 10.2.1.5, Eth1/1, [0/0], 01:19:48, local
10.2.2.4/31, ubest/mbest: 1/0, attached
    *via 10.2.2.5, Eth1/2, [0/0], 01:19:46, direct
10.2.2.5/32, ubest/mbest: 1/0, attached
    *via 10.2.2.5, Eth1/2, [0/0], 01:19:46, local
10.4.2.0/31, ubest/mbest: 1/0, attached
    *via 10.4.2.1, Eth1/6, [0/0], 01:19:45, direct
10.4.2.1/32, ubest/mbest: 1/0, attached
    *via 10.4.2.1, Eth1/6, [0/0], 01:19:45, local
10.4.3.0/31, ubest/mbest: 1/0, attached
    *via 10.4.3.1, Eth1/7, [0/0], 01:19:45, direct
10.4.3.1/32, ubest/mbest: 1/0, attached
    *via 10.4.3.1, Eth1/7, [0/0], 01:19:45, local
```