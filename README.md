# CCNA-Lab-Inter-Vlan-Routing and DHCP
Inter-VLAN routing lab with router-on-a-stick configuration
-----
## Lab Overview
This lab demostrates:
- VLAN configuration
- Trunking
- Router-on-a-Stick
- DHCP configuration
- Inter-VLAN communication testing
## VLAN & IP Design
Department	VLAN	Subnet	Gateway
Management	10	192.168.10.0/24	192.168.10.1
Finance	20	192.168.20.0/24	192.168.20.1
HR	30	192.168.30.0/24	192.168.30.1
IT	40	192.168.40.0/24	192.168.40.1
Guest	50	192.168.50.0/24	192.168.50.1

## Core Switch Configuration
enable
conf t

vlan 10
 name HR
vlan 20
 name SALES
vlan 30
 name IT
vlan 40
 name GuesNetwork

interface-range fa0/1-8
 switchport mode access
 switchport access vlan 10

interface-range fa0/9-16
 switchport mode access
 switchport access vlan 20

interface fa0/17-20
 switchport mode access
 switchport access vlan 30

interface fa0/22
 switchport mode access
 switchport access vlan 40



interface gig0/1
 switchport mode trunk

end
########################
## Router Configuration (Router-on-a-Stick)
enable
conf t

interface g0/1
 no shutdown

interface g0/1.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0

interface g0/1.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0

interface g0/1.30
 encapsulation dot1Q 30
 ip address 192.168.30.1 255.255.255.0

interface g0/1.40
 encapsulation dot1Q 40
 ip address 192.168.40.1 255.255.255.0

end
##############################
## Router Verification Commands
show ip interface brief
show ip route
## Switch Verification Commands
show vlan brief
show interfaces trunk
############################
## We now need to configure DHCP on the router to assign IP Addresses to our respective Vlans.
## Goal
-Router provides DHCP for:
-VLAN 10 – HR
-VLAN 20 – SALES
-VLAN 30 – IT
-VLAN 40 – GuestNetwork
-Exclude gateway addresses
-PCs obtain IP automatically
## Important Note
Since we already created subinterfaces like:
G0/1.10 → 192.168.10.1
G0/1.20 → 192.168.20.1 etc.
The router can now serve DHCP pools for each subnet.
## Router Configurations
enable
conf t
ip dhcp excluded-address 192.168.10.1
ip dhcp excluded-address 192.168.20.1
ip dhcp excluded-address 192.168.30.1
ip dhcp excluded-address 192.168.40.1
## If you want to exclude a range of IPs for your printers, servers and IoTs, use:
ip dhcp excluded-address 192.168.10.1 192.168.10.20 (This excludes IPs from 1-20 and DHCP will start to offer from .21)

## Create DHCP Pools as follows
## VLAN 10 HR
ip dhcp pool HR
network 192.168.10.0 255.255.255.0
default-router 192.168.10.1
dns-server 8.8.8.8
domain-name cloud.local

## VLAN 20 SALES
ip dhcp pool Sales
network 192.168.20.0 255.255.255.0
default-router 192.168.20.1
dns-server 8.8.8.8
domain-name cloud.local

## VLAN 30 IT (It is against IT operation standard to set your servers to receive DHCP IP, static or manaual addressing is recommended)
ip dhcp pool HR
network 192.168.30.0 255.255.255.0
default-router 192.168.30.1
dns-server 8.8.8.8
domain-name cloud.local

## VLAN 40 GuestNetwork
ip dhcp pool GuestNetwork
network 192.168.40.0 255.255.255.0
default-router 192.168.40.1
dns-server 8.8.8.8
domain-name cloud.local

#####################################
## Now configure your PCs to receive DHCP Addresses
On each PC in Packet Tracer:
Click PC
Desktop
IP Configuration
Select DHCP
## Verification
show ip dhcp binding
show ip dhcp pool

## CONGRATULATIONS !!!
# VLAN segmentation ✅
# 802.1Q trunking ✅
# Router-on-a-Stick ✅
# Inter-VLAN routing ✅
# DHCP services ✅

