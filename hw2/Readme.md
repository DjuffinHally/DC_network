# Homework 1

![](https://github.com/DjuffinHally/DC_network/blob/main/hw2/netscheme.png)

## Адресный план

`IP = 10.Dn.Sn.X/31`, где:
Dn – номер ЦОДа,
Sn – номер spine / leaf,
X – значение по порядку,

Dn для DC1 = 0 – 7, где
0 – loopback spine
1 – loopback leaf
2 – p2p links
3 – reserved
4-7 – services

Для loopback
```
10.[0-1].[1-255].[0-255]
  |   |   |- номер loopback
  |   |- номер spine или leaf коммутатора
  |-> 0 - spine
  |-> 1 - leaf
```

### Примеры
10.0.1.1/32 - loopback1-spine1
10.1.3.2/32 - loopback2-leaf3
10.2.2.4/31 - p2p_spine2-leaf3
10.4.0.0/14 - services

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

interface loopback0
  description ID
  ip address 10.0.2.0/32
```
