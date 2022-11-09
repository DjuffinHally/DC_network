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
feature isis
router isis UNDERLAY
net 49.0011.0001.0001.0001.00
is-type level-1-2

interface Ethernet1/1
  description to_leaf_1
  ip address 10.2.1.0/31
  ip router isis UNDERLAY
  no switchport
  no shutdown

interface Ethernet1/2
  description to_leaf_2
  ip address 10.2.1.2/31
  ip router isis UNDERLAY
  no switchport
  no shutdown

interface Ethernet1/3
  description to_leaf_3
  ip address 10.2.1.4/31
  ip router isis UNDERLAY
  no switchport
  no shutdown

interface loopback0
  description ID
  ip address 10.0.1.0/32
```

### spine_2
```
feature isis
router isis UNDERLAY
net 49.0011.0001.0001.0002.00
is-type level-1-2

interface Ethernet1/1
  description to_leaf_1
  ip address 10.2.2.0/31
  ip router isis UNDERLAY
  no switchport
  no shutdown

interface Ethernet1/2
  description to_leaf_2
  ip address 10.2.2.2/31
  ip router isis UNDERLAY
  no switchport
  no shutdown

interface Ethernet1/3
  description to_leaf_3
  ip address 10.2.2.4/31
  ip router isis UNDERLAY
  no switchport
  no shutdown

interface loopback0
  description ID
  ip address 10.0.2.0/32
```

### leaf_1
```
feature isis
router isis UNDERLAY
net 49.0011.0001.0002.0001.00
is-type level-1-2

interface Ethernet1/1
  description to_spine_1
  ip address 10.2.1.1/31
  ip router isis UNDERLAY
  no switchport
  no shutdown

interface Ethernet1/2
  description to_spine_2
  ip address 10.2.2.1/31
  ip router isis UNDERLAY
  no switchport
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
  ip router isis UNDERLAY
```

### leaf_2
```
feature isis
router isis UNDERLAY
net 49.0011.0001.0002.0002.00
is-type level-1-2

interface Ethernet1/1
  description to_spine_1
  ip address 10.2.1.3/31
  ip router isis UNDERLAY
  no switchport
  no shutdown

interface Ethernet1/2
  description to_spine_2
  ip address 10.2.2.3/31
  ip router isis UNDERLAY
  no switchport
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
  ip router isis UNDERLAY
```

### leaf_3
```
feature ospf

feature isis
router isis UNDERLAY
net 49.0011.0001.0002.0003.00
is-type level-1-2

interface Ethernet1/1
  description to_spine_1
  ip address 10.2.1.5/31
  ip router isis UNDERLAY
  no switchport
  no shutdown

interface Ethernet1/2
  description to_spine_2
  ip address 10.2.2.5/31
  ip router isis UNDERLAY
  no switchport
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
  ip address 10.1.3.0/32

interface loopback1
  description VTEP
  ip address 10.1.3.1/32
  ip router isis UNDERLAY
```

## Проверка работоспособности

```
spine_1# sh isis database
IS-IS Process: UNDERLAY LSP database VRF: default
IS-IS Level-1 Link State Database
  LSPID                 Seq Number   Checksum  Lifetime   A/P/O/T
  spine_1.00-00       * 0x0000001F   0x05E0    1000       0/0/0/3
  spine_2.00-00         0x0000001F   0x7664    995        0/0/0/3
  leaf_1.00-00          0x00000012   0xC73D    792        0/0/0/3
  leaf_1.01-00          0x00000006   0x932B    792        0/0/0/3
  leaf_1.02-00          0x00000006   0x9527    792        0/0/0/3
  leaf_2.00-00          0x00000013   0x0DE7    928        0/0/0/3
  leaf_2.01-00          0x00000001   0xB20E    924        0/0/0/3
  leaf_2.02-00          0x00000001   0xA21E    927        0/0/0/3
  leaf_3.00-00          0x00000011   0x588F    1000       0/0/0/3
  leaf_3.01-00          0x00000001   0xB50A    999        0/0/0/3
  leaf_3.02-00          0x00000001   0xB706    994        0/0/0/3

IS-IS Level-2 Link State Database
  LSPID                 Seq Number   Checksum  Lifetime   A/P/O/T
  spine_1.00-00       * 0x0000001F   0x05E0    996        0/0/0/3
  spine_2.00-00         0x00000020   0x7465    998        0/0/0/3
  leaf_1.00-00          0x00000012   0xC73D    792        0/0/0/3
  leaf_1.01-00          0x00000006   0x932B    792        0/0/0/3
  leaf_1.02-00          0x00000006   0x9527    792        0/0/0/3
  leaf_2.00-00          0x00000013   0x0DE7    927        0/0/0/3
  leaf_2.01-00          0x00000001   0xB20E    927        0/0/0/3
  leaf_2.02-00          0x00000001   0xA21E    926        0/0/0/3
  leaf_3.00-00          0x00000011   0x36B1    1000       0/0/0/3
  leaf_3.01-00          0x00000001   0xB50A    995        0/0/0/3
  leaf_3.02-00          0x00000001   0xB706    999        0/0/0/3

spine_1#    sh ip route isis
IP Route Table for VRF "default"
'*' denotes best ucast next-hop
'**' denotes best mcast next-hop
'[x/y]' denotes [preference/metric]
'%<string>' in via output denotes VRF <string>

10.1.1.1/32, ubest/mbest: 1/0
    *via 10.2.1.1, Eth1/1, [115/41], 00:07:01, isis-UNDERLAY, L1
10.1.2.1/32, ubest/mbest: 1/0
    *via 10.2.1.3, Eth1/2, [115/41], 00:04:35, isis-UNDERLAY, L1
10.1.3.1/32, ubest/mbest: 1/0
    *via 10.2.1.5, Eth1/3, [115/41], 00:03:22, isis-UNDERLAY, L1
10.2.2.0/31, ubest/mbest: 1/0
    *via 10.2.1.1, Eth1/1, [115/80], 00:10:38, isis-UNDERLAY, L1
10.2.2.2/31, ubest/mbest: 1/0
    *via 10.2.1.3, Eth1/2, [115/80], 00:04:35, isis-UNDERLAY, L1
10.2.2.4/31, ubest/mbest: 1/0
    *via 10.2.1.5, Eth1/3, [115/80], 00:03:22, isis-UNDERLAY, L1
```