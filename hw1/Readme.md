# Homework 1

![](https://github.com/DjuffinHally/DC_network/blob/main/hw1/netscheme.png)

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


```              sp-1                sp-2
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

## spine_1
```
hostname spine_1
interface Ethernet1/1
  no switchport
  ip address 10.2.1.0/31
  no shutdown

interface Ethernet1/2
  no switchport
  ip address 10.2.1.2/31
  no shutdown

interface Ethernet1/3
  no switchport
  ip address 10.2.1.4/31
  no shutdown

interface loopback0
  ip address 10.0.1.0/32
```

### spine_2
```
hostname spine_2
boot nxos bootflash:nxos.9.3.3.bin
interface Ethernet1/1
  no switchport
  ip address 10.2.2.0/31
  no shutdown

interface Ethernet1/2
  no switchport
  ip address 10.2.2.2/31
  no shutdown

interface Ethernet1/3
  no switchport
  ip address 10.2.2.4/31
  no shutdown

interface loopback0
  ip address 10.0.2.0/32
```

###leaf_1
```
hostname leaf_1
boot nxos bootflash:nxos.9.3.3.bin
interface Ethernet1/1
  no switchport
  ip address 10.2.1.1/31
  no shutdown

interface Ethernet1/2
  no switchport
  ip address 10.2.2.1/31
  no shutdown

interface loopback0
  ip address 10.1.1.0/32
```

###leaf_2
```
hostname leaf_2
boot nxos bootflash:nxos.9.3.3.bin
interface Ethernet1/1
  no switchport
  ip address 10.2.1.3/31
  no shutdown

interface Ethernet1/2
  no switchport
  ip address 10.2.2.3/31
  no shutdown

interface loopback0
  ip address 10.1.2.0/32
```

###leaf_3
```
hostname leaf_3
boot nxos bootflash:nxos.9.3.3.bin
interface Ethernet1/1
  no switchport
  ip address 10.2.1.5/31
  no shutdown

interface Ethernet1/2
  no switchport
  ip address 10.2.2.5/31
  no shutdown

interface loopback0
  ip address 10.1.3.0/32
```
