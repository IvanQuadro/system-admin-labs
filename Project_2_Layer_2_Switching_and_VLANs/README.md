![image](https://github.com/user-attachments/assets/04758df5-51e9-4045-b3dd-6644fa5200f8)# VTP Network Configuration Project

## Overview

This repository contains the configuration and documentation for a network topology project focused on VLAN Trunking Protocol (VTP) with Router on a Stick (ROAS). The setup includes a VTP server (Switch SW2), two VTP clients (Switches SW1 and SW3), and a router (R1) configured for ROAS to handle inter-VLAN routing. Multiple PCs are distributed across different VLANs, managed centrally via VTP. Ideal for learning VTP, ROAS, and VLAN management.

---

## 🧭 Topology

![Network Topology](.Topology)

---

- **R1 (Router, Green):**
  - Central router for inter-VLAN routing
  - `F0/5`: Trunk to R1
  - `F0/4`: Trunk to SW3
  - `F0/3`: Trunk to SW1

- **SW1 (VTP Client, Yellow):**
  - `F0/3`: Trunk to R1
  - PCs:
    - PC1: 192.168.10.10 (VLAN 10)
    - PC3: 192.168.20.10 (VLAN 20)

- **SW2 (VTP Server, Green):**
  - `F0/3`: Trunk to R1
  - Manages VTP domain and VLAN propagation
  -  PCs:
    - PC2: 192.168.10.11 (VLAN 10)
    - PC5: 192.168.30.10 (VLAN 30)

- **SW3 (VTP Client, Yellow):**
  - `F0/3`: Trunk to R1
  - PCs:
    - PC4: 192.168.20.11 (VLAN 20)
    - PC6: 192.168.30.11 (VLAN 30)

- **VTP Details:**
  - Domain Name: `LAB1`
  - Password: `LAB1`
  - VLANs:
    - VLAN 10: 192.168.10.0/24
    - VLAN 20: 192.168.20.0/24
    - VLAN 30: 192.168.30.0/24
    - VLAN 40: 192.168.40.0/24

## Features
- VTP for centralized VLAN management
- Trunk links for VLAN propagation
- Inter-VLAN routing via R1

## Getting Started

### Prerequisites
- Cisco Packet Tracer, GNS3, or similar network simulator
- Basic understanding of VTP, VLANs, and routing

### Installation
1. Clone the repository:
   ```bash
   git clone https://github.com/your-username/vtp-network-project.git

### Configuration
📍 Router R1 (ROAS):

```bash

 interface G0/0
 no shutdown

interface G0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0

interface G0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0

interface G0/0.30
 encapsulation dot1Q 30
 ip address 192.168.30.1 255.255.255.0

interface G0/0.40
 encapsulation dot1Q 40
 ip address 192.168.40.1 255.255.255.0

```

📍 Switch SW2 (VTP Server):

```bash

vtp mode server
vtp domain LAB1
vtp password LAB1

interface F0/3
 switchport mode trunk
interface F0/4
 switchport mode trunk
interface F0/5
 switchport mode trunk

vlan 10
 name ADMINISTRATION 
vlan 20
 name SALES
vlan 30
 name IT
vlan 40
 name MANAGEMENT

```

📍 Switch SW1 (VTP Client):

```bash

vtp mode client
vtp domain LAB1
vtp password LAB1

interface F0/3
 switchport mode trunk

interface F0/1
 switchport mode access
 switchport access vlan 10

interface F0/2
 switchport mode access
 switchport access vlan 20

```

📍 Switch SW3 (VTP Client):


```bash

vtp mode client
vtp domain LAB1
vtp password LAB1

interface F0/4
 switchport mode trunk

interface F0/1
 switchport mode access
 switchport access vlan 20

interface F0/2
 switchport mode access
 switchport access vlan 30

```

📍 Contributing

Contributions are welcome! Please fork the repository and submit pull requests with improvements or additional configurations.

📍 Usage

Test VTP synchronization:

```bash

show vtp status

```

Verify VLAN creation:

```bash

show vlan brief


```

Test inter-VLAN connectivity:

Example: ping 192.168.10.11 from PC1 to PC2

Verify ROAS routing:

```bash

show ip route
show ip interface brief

```

### Notes

R1 is only connected to SW2 via F0/5, which propagates VLANs to SW1 and SW3.

Ensure all PCs use the correct gateway:

VLAN 10 → 192.168.10.1

VLAN 20 → 192.168.20.1

VLAN 30 → 192.168.30.1

VLAN 40 → 192.168.40.1

ROAS uses subinterfaces; no ip routing command is needed.

### Contributing
Contributions are welcome!
Fork the repository and submit pull requests for improvements or additional labs.

### Acknowledgments
Inspired by Cisco’s official documentation and best practices in networking.
