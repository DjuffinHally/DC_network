# Homework 2

![](https://github.com/DjuffinHally/DC_network/blob/main/hw2/netscheme.png)

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
feature ospf

router ospf UNDERLAY
  router-id 10.1.1.0

interface Ethernet1/1
  no ip redirects
  description to_spine_1
  ip ospf network point-to-point
  no switchport
  ip address 10.2.1.1/31
  ip router ospf UNDERLAY area 0.0.0.0
  no shutdown

interface Ethernet1/2
  no ip redirects
  description to_spine_2
  ip ospf network point-to-point
  no switchport
  ip address 10.2.2.1/31
  ip router ospf UNDERLAY area 0.0.0.0
  no shutdown

interface loopback0
  description ID
  ip address 10.1.1.0/32

interface loopback1
  description VTEP
  ip address 10.1.1.1/32
  ip router ospf UNDERLAY area 0.0.0.0
```

### spine_2
```
feature ospf

router ospf UNDERLAY
  router-id 10.1.2.0


interface Ethernet1/1
  description to_spine_1
  no ip redirects
  ip ospf network point-to-point
  no switchport
  ip address 10.2.1.3/31
  ip router ospf UNDERLAY area 0.0.0.0
  no shutdown

interface Ethernet1/2
  description to_spine_2
  no ip redirects
  ip ospf network point-to-point
  no switchport
  ip address 10.2.2.3/31
  ip router ospf UNDERLAY area 0.0.0.0
  no shutdown

interface loopback0
  description ID
  ip address 10.1.2.0/32

interface loopback1
  description VTEP
  ip address 10.1.2.1/32
  ip router ospf UNDERLAY area 0.0.0.0
```

### leaf_1
```
feature ospf

router ospf UNDERLAY
  router-id 10.1.1.0

interface Ethernet1/1
  no ip redirects
  description to_spine_1
  ip ospf network point-to-point
  no switchport
  ip address 10.2.1.1/31
  ip router ospf UNDERLAY area 0.0.0.0
  no shutdown

interface Ethernet1/2
  no ip redirects
  description to_spine_2
  ip ospf network point-to-point
  no switchport
  ip address 10.2.2.1/31
  ip router ospf UNDERLAY area 0.0.0.0
  no shutdown

interface Ethernet1/6
  no switchport
  ip address 10.4.0.1/31
  no shutdown

interface loopback0
  description ID
  ip address 10.1.1.0/32

interface loopback1
  description VTEP
  ip address 10.1.1.1/32
  ip router ospf UNDERLAY area 0.0.0.0
```

### leaf_2
```
feature ospf

router ospf UNDERLAY
  router-id 10.1.2.0


interface Ethernet1/1
  description to_spine_1
  no ip redirects
  ip ospf network point-to-point
  no switchport
  ip address 10.2.1.3/31
  ip router ospf UNDERLAY area 0.0.0.0
  no shutdown

interface Ethernet1/2
  description to_spine_2
  no ip redirects
  ip ospf network point-to-point
  no switchport
  ip address 10.2.2.3/31
  ip router ospf UNDERLAY area 0.0.0.0
  no shutdown

interface Ethernet1/6
  no switchport
  ip address 10.4.1.1/31
  no shutdown

interface loopback0
  description ID
  ip address 10.1.2.0/32

interface loopback1
  description VTEP
  ip address 10.1.2.1/32
  ip router ospf UNDERLAY area 0.0.0.0
```

### leaf_3
```
feature ospf

router ospf UNDERLAY
  router-id 10.0.2.0


interface Ethernet1/1
  description to_leaf_1
  no ip redirects
  ip ospf network point-to-point
  no switchport
  ip address 10.2.2.0/31
  ip router ospf UNDERLAY area 0.0.0.0
  no shutdown

interface Ethernet1/2
  description to_leaf_1
  no ip redirects
  ip ospf network point-to-point
  no switchport
  ip address 10.2.2.2/31
  ip router ospf UNDERLAY area 0.0.0.0
  no shutdown

interface Ethernet1/3
  description to_leaf_1
  no ip redirects
  ip ospf network point-to-point
  no switchport
  ip address 10.2.2.4/31
  ip router ospf UNDERLAY area 0.0.0.0
  no shutdown

interface Ethernet1/6
  no switchport
  ip address 10.4.2.1/31
  no shutdown

interface Ethernet1/7
  no switchport
  ip address 10.4.3.1/31
  no shutdown

interface loopback0
  description ID
  ip address 10.0.2.0/32
```

## Проверка работоспособности

```
spine_1# sh ip ospf database
        OSPF Router with ID (10.0.1.0) (Process ID UNDERLAY VRF default)

                Router Link States (Area 0.0.0.0)

Link ID         ADV Router      Age        Seq#       Checksum Link Count
10.0.1.0        10.0.1.0        1283       0x80000014 0xb5ba   6
10.0.2.0        10.0.2.0        1279       0x80000011 0xa0ca   6
10.1.1.0        10.1.1.0        1278       0x8000000d 0x27cf   5
10.1.2.0        10.1.2.0        1129       0x800000a5 0x0251   5
10.1.3.0        10.1.3.0        1397       0x8000000a 0xbd26   5

spine_1# sh ip route ospf
IP Route Table for VRF "default"
'*' denotes best ucast next-hop
'**' denotes best mcast next-hop
'[x/y]' denotes [preference/metric]
'%<string>' in via output denotes VRF <string>

10.1.1.1/32, ubest/mbest: 1/0
    *via 10.2.1.1, Eth1/1, [110/41], 00:21:23, ospf-UNDERLAY, intra
10.1.2.1/32, ubest/mbest: 1/0
    *via 10.2.1.3, Eth1/2, [110/41], 00:49:23, ospf-UNDERLAY, intra
10.1.3.1/32, ubest/mbest: 1/0
    *via 10.2.1.5, Eth1/3, [110/41], 00:23:21, ospf-UNDERLAY, intra
10.2.2.0/31, ubest/mbest: 1/0
    *via 10.2.1.1, Eth1/1, [110/80], 00:21:23, ospf-UNDERLAY, intra
10.2.2.2/31, ubest/mbest: 1/0
    *via 10.2.1.3, Eth1/2, [110/80], 00:49:23, ospf-UNDERLAY, intra
10.2.2.4/31, ubest/mbest: 1/0
    *via 10.2.1.5, Eth1/3, [110/80], 00:23:21, ospf-UNDERLAY, intra
```