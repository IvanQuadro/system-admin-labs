# Project: Cisco Multilayer Switch Network with VTP, EtherChannel, HSRP, OSPF, and Inter-VLAN Routing


## 📋 Overview

This project demonstrates how to build a resilient, scalable enterprise network using Cisco multilayer switches and routers. Key features include:

VLAN segmentation (Engineering, HR, Finance, Admin)

VTP domain for VLAN propagation

EtherChannel between distribution switches for redundancy and bandwidth

HSRP for gateway redundancy

Inter-VLAN routing via SVIs on multilayer switches

Router-on-a-stick interconnection and OSPF dynamic routing

## 🧭 Topology


```bash



```


### Devices:

2 Distribution Switches: DistSW1, DistSW2 (Multilayer Switches)

2 Access Switches (Optional if adding more VPCs)

1 Router: R1 (acts as gateway to Internet)

8 PCs: 2 per VLAN (VLANs 10, 20, 30, 40)

## 🧱 CONFIGURATION

### 🔌 Step 1: Trunk & EtherChannel Between DistSW1 and DistSW2
On both switches:

```bash

interface range e1/0 - 1
switchport trunk encapsulation dot1q
switchport mode trunk
channel-group 1 mode active
exit

```

```bash

interface port-channel1
switchport mode trunk
exit

```

### 🧮 Step 2: VTP Configuration

On DistSW1 (VTP Server):

```bash

vtp domain LAB
vtp mode server
vtp password LAB


```
On DistSW2 (VTP Client):

```bash

vtp domain NETWORK
vtp mode client
vtp password cisco

```

## 🟨 Step 3: VLAN and SVI Creation

On both DistSW1 and DistSW2:

```bash

vlan 10
name Engineering
vlan 20
name HR
vlan 30
name Finance
vlan 40
name Admin
exit

```

### Now create SVIs on DistSW1:

```bash

interface vlan 10
ip address 10.1.1.254 255.255.255.0
no shutdown
exit

interface vlan 20
ip address 20.1.1.254 255.255.255.0
no shutdown
exit

interface vlan 30
ip address 30.1.1.254 255.255.255.0
no shutdown
exit

interface vlan 40
ip address 40.1.1.254 255.255.255.0
no shutdown
exit


```

### Same on DistSW2, but use .253:

```bash

interface vlan 10
ip address 10.1.1.253 255.255.255.0
no shutdown
exit

interface vlan 20
ip address 20.1.1.253 255.255.255.0
no shutdown
exit

interface vlan 30
ip address 30.1.1.253 255.255.255.0
no shutdown
exit

interface vlan 40
ip address 40.1.1.253 255.255.255.0
no shutdown
exit

```

## 🧷 Step 4: HSRP Configuration

### On DistSW1:

```bash


interface vlan 10
standby 10 ip 10.1.1.100
exit
interface vlan 20
standby 20 ip 20.1.1.100
exit
interface vlan 30
standby 30 ip 30.1.1.100
exit
interface vlan 40
standby 40 ip 40.1.1.100
exit

```

### On DistSW2:

```bash

interface vlan 10
standby 10 ip 10.1.1.100
exit
interface vlan 20
standby 20 ip 20.1.1.100
exit
interface vlan 30
standby 30 ip 30.1.1.100
exit
interface vlan 40
standby 40 ip 40.1.1.100
exit

```

### Optional: Increase priority on DistSW2 to make it Active:

```bash

interface vlan 10
standby 10 priority 110
standby 10 preempt
exit

```
... repeat for vlan 20, 30, 40 ...


## 🌐 Step 5: L3 Links Between Router and Switches

On DistSW1:

```bash

interface e0/0
no switchport
ip address 12.1.1.1 255.255.255.0
no shutdown

```

On DistSW2:

```bash
interface e0/0
no switchport
ip address 13.1.1.1 255.255.255.0
no shutdown

```

On Router R1:

```bash

interface f0/0
ip address 12.1.1.2 255.255.255.0
no shutdown
exit
interface f0/1
ip address 13.1.1.2 255.255.255.0
no shutdown


```

🧭 Step 6: OSPF Routing


On Router R1:

```bash

router ospf 1
network 12.1.1.0 0.0.0.255 area 0
network 13.1.1.0 0.0.0.255 area 0

```

On DistSW1:

```bash

router ospf 1
network 12.1.1.0 0.0.0.255 area 0
network 10.1.1.0 0.0.0.255 area 0
network 20.1.1.0 0.0.0.255 area 0
network 30.1.1.0 0.0.0.255 area 0
network 40.1.1.0 0.0.0.255 area 0

```

On DistSW2:

```bash

router ospf 1
network 13.1.1.0 0.0.0.255 area 0
network 10.1.1.0 0.0.0.255 area 0
network 20.1.1.0 0.0.0.255 area 0
network 30.1.1.0 0.0.0.255 area 0
network 40.1.1.0 0.0.0.255 area 0

```

🖥️ Step 7: Configure VPCs (PCs)

Example config for VPC1 (VLAN 10):

```bash

ip 10.1.1.1 255.255.255.0 10.1.1.100

```

Example config for VPC3 (VLAN 20):

```bash

ip 20.1.1.1 255.255.255.0 20.1.1.100
Repeat for VLANs 30, 40. Use .2, .3, etc., for other PCs.

```

✅ Verification

Test the following:

ping 10.1.1.100 from PC in VLAN 10

ping 20.1.1.1 from VLAN 10 PC (inter-VLAN)

ping 12.1.1.2 and 13.1.1.2 from switches

show standby brief on switches

show ip ospf neighbor on router/switches
