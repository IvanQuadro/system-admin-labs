# 🔒 Enterprise Lab Network Security Configuration

This README documents the security features implemented in an enterprise lab network to simulate realistic, enterprise-grade security controls on Layer 2 and Layer 3 devices.  
**Management security and device hardening are excluded from this documentation.**  
The goal is to enforce a Zero Trust model, protect against common Layer 2/3 attacks, and ensure network stability and scalability.




---

## 📑 Table of Contents
1. [VLAN Segmentation & Inter-VLAN ACLs (Zero Trust)](#1-vlan-segmentation--inter-vlan-acls-zero-trust)  
2. [DHCP Snooping](#2-dhcp-snooping)  
3. [Dynamic ARP Inspection (DAI)](#3-dynamic-arp-inspection-dai)  
4. [Port Security](#4-port-security)  
5. [BPDU Guard & STP PortFast](#5-bpdu-guard--stp-portfast)  
6. [Storm Control](#6-storm-control)  
7. [Trusted Uplink Ports](#7-trusted-uplink-ports)  
8. [Summary of Security Features](#8-summary-of-security-features)  

---

## 1. VLAN Segmentation & Inter-VLAN ACLs (Zero Trust)

**Purpose:** Enforce Zero Trust by explicitly controlling inter-VLAN communication using Extended Access Control Lists (ACLs).  
ACLs are applied **inbound** on each Switched Virtual Interface (SVI) of distribution (multilayer) switches.

```bash
ip access-list extended VLAN10_IN
 deny ip 10.1.1.0 0.0.0.255 20.1.1.0 0.0.0.255
 deny ip 10.1.1.0 0.0.0.255 30.1.1.0 0.0.0.255
 deny ip 10.1.1.0 0.0.0.255 40.1.1.0 0.0.0.255
 permit ip any any

interface vlan10
 ip access-group VLAN10_IN in
```

✅ Prevents direct communication between VLANs.
✅ Only explicitly permitted traffic (e.g., Internet-bound) is allowed.

2. DHCP Snooping
Purpose: Prevent rogue DHCP servers from assigning malicious IP addresses.
Enabled on all switches for VLANs 10, 20, 30, and 40.
Only uplink/trunk interfaces to trusted devices (MS1, MS2, Core) are trusted.

```bash
Copia codice
ip dhcp snooping
ip dhcp snooping vlan 10,20,30,40

interface fa0/5
 ip dhcp snooping trust
```
✅ Ensures DHCP replies are only accepted from trusted ports.
✅ Mitigates unauthorized DHCP server attacks.

3. Dynamic ARP Inspection (DAI)
Purpose: Mitigate ARP spoofing and Man-in-the-Middle (MitM) attacks.
Enabled for VLANs 10, 20, 30, and 40.
Trusted ports explicitly defined; access ports remain untrusted.

bash
Copia codice
ip arp inspection vlan 10,20,30,40

interface fa0/1
 ip arp inspection limit rate 30

interface fa0/5
 ip arp inspection trust
✅ Validates ARP packets against DHCP Snooping bindings.
✅ Protects against ARP poisoning.

4. Port Security
Purpose: Restrict the number of devices per access port.
Applied on all access ports with a maximum of 2 MAC addresses.

```bash
Copia codice
interface range fa0/1 - 24
 switchport mode access
 switchport port-security
 switchport port-security maximum 2
 switchport port-security violation restrict
 switchport port-security mac-address sticky
```
✅ Limits devices per port.
✅ Restricts unknown MAC addresses.
✅ Dynamically learns and saves MAC addresses.

5. BPDU Guard & STP PortFast
Purpose: Protect STP topology and speed up convergence for host ports.

```bash
Copia codice
interface range fa0/1 - 24
 spanning-tree bpduguard enable
 spanning-tree portfast
```
✅ Prevents rogue switches in STP.
✅ Reduces convergence time.

6. Storm Control
Purpose: Mitigate broadcast and multicast storms.
Applied on all access ports, limiting traffic to 5% of bandwidth.

```bash
Copia codice
interface range fa0/1 - 24
 storm-control broadcast level 5.00
 storm-control multicast level 5.00
```
✅ Drops excessive broadcast/multicast traffic.
✅ Protects network stability.

7. Trusted Uplink Ports
Purpose: Ensure correct functionality of control-plane protocols.
Trunk/uplink ports to distribution switches and routers are marked as trusted.

```bash
Copia codice
interface fa0/24
 switchport mode trunk
 ip dhcp snooping trust
 ip arp inspection trust
```
✅ Prevents false positives on uplinks.
✅ Ensures smooth inter-switch communication.

8. Summary of Security Features
Feature	Layer	Purpose
Inter-VLAN ACLs	L3	Enforce Zero Trust segmentation
DHCP Snooping	L2	Block rogue DHCP servers
Dynamic ARP Inspection	L2	Prevent ARP spoofing attacks
Port Security	L2	Limit devices per port
BPDU Guard + PortFast	L2	Protect STP, faster convergence
Storm Control	L2	Prevent broadcast/multicast storms
Trusted Uplink Ports	L2/L3	Secure trunk and uplink communication
