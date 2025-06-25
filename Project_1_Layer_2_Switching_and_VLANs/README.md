# VTP Network Configuration Project

## Overview

This repository contains the configuration and documentation for a network topology project focused on VLAN Trunking Protocol (VTP). The setup includes a VTP server (Router R1) and two VTP clients (Switches SW1 and SW2), with multiple PCs across different VLANs. This is ideal for learning VTP, VLAN management, trunking, and inter-VLAN routing.

## Topology

- **R1 (VTP Server, Green):**
  - `G0/0`: Central routing interface
  - `F0/5`: Trunk to SW1
  - `F0/4`: Trunk to SW2
  - Manages VLAN propagation and routing

- **SW1 (VTP Client, Yellow):**
  - `F0/3`: Trunk to R1
  - PCs:
    - **PC1** – IP: `192.168.10.10` (VLAN 10)
    - **PC2** – IP: `192.168.20.10` (VLAN 20)

- **SW2 (VTP Client, Yellow):**
  - `F0/3`: Trunk to R1
  - PCs:
    - **PC3** – IP: `192.168.10.11` (VLAN 10)
    - **PC4** – IP: `192.168.30.10` (VLAN 30)
    - **PC5** – IP: `192.168.40.11` (VLAN 40)
    - **PC6** – IP: `192.168.30.11` (VLAN 30)

### VLANs

| VLAN ID | Subnet            | Purpose      |
|---------|-------------------|--------------|
| 10      | 192.168.10.0/24   | Shared VLAN  |
| 20      | 192.168.20.0/24   | SW1 Only     |
| 30      | 192.168.30.0/24   | SW2 Only     |
| 40      | 192.168.40.0/24   | SW2 Only     |

## Features

- VTP for centralized VLAN management
- Trunk links for VLAN propagation
- Inter-VLAN routing via router-on-a-stick (R1)

## Getting Started

### Prerequisites

- Cisco Packet Tracer, GNS3 o altro simulatore
- Conoscenze base di VTP, VLAN, trunking e routing

### Installazione

1. Clona il repository:
   ```bash
   git clone https://github.com/your-username/vtp-network-project.git

### Router R1 (VTP Server)

vtp mode server
vtp domain MYDOMAIN
vtp password MYPASSWORD

interface F0/5
 switchport mode trunk
interface F0/4
 switchport mode trunk

ip routing

interface vlan 10
 ip address 192.168.10.1 255.255.255.0
interface vlan 20
 ip address 192.168.20.1 255.255.255.0
interface vlan 30
 ip address 192.168.30.1 255.255.255.0
interface vlan 40
 ip address 192.168.40.1 255.255.255.0

### Switch SW1 (VTP Client)

vtp mode client
vtp domain MYDOMAIN
vtp password MYPASSWORD

interface F0/3
 switchport mode trunk

interface F0/1
 switchport mode access
 switchport access vlan 10

interface F0/2
 switchport mode access
 switchport access vlan 20

### Switch SW1 (VTP Client)
