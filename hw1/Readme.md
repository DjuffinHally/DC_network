# Homework 1

![](https://github.com/DjuffinHally/DC_network/tree/main/hw1#:~:text=8%20minutes%20ago-,netscheme.png,-commit)

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
