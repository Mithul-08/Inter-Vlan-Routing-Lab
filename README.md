# Inter-VLAN Routing Lab — Router-on-a-Stick

A small Cisco Packet Tracer lab demonstrating inter-VLAN routing using the **router-on-a-stick** method, with two VLANs, a Layer 2 switch, and a single router handling routing between subnets via sub-interfaces.

## Topology

```
        VLAN 10 (192.168.10.0/24)          VLAN 20 (192.168.20.0/24)
        ┌───────────┐                       ┌───────────┐
        │ PC0, PC1  │                       │ PC2, PC3  │
        └─────┬─────┘                       └─────┬─────┘
              │                                    │
              └───────────────┬────────────────────┘
                               │
                        ┌──────┴──────┐
                        │  CS-SW      │  
                        │  Switch     │
                        └──────┬──────┘
                               │ Trunk (VLAN 10 & 20)
                        ┌──────┴──────┐
                        │   CSR       │ 
                        │  Router     │
                        └─────────────┘
```

## Devices

| Device | Role |
|---|---|
| CS-SW | Access/Distribution Switch |
| CSR | Router (inter-VLAN routing) |
| PC0, PC1 | End hosts — VLAN 10 |
| PC2, PC3 | End hosts — VLAN 20 |

## IP Addressing

| Segment | Network | Gateway |
|---|---|---|
| VLAN 10 | 192.168.10.0/24 | 192.168.10.1 |
| VLAN 20 | 192.168.20.0/24 | 192.168.20.1 |

| Host | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|
| PC0 | 192.168.10.10 | 255.255.255.0 | 192.168.10.1 |
| PC1 | 192.168.10.11 | 255.255.255.0 | 192.168.10.1 |
| PC2 | 192.168.20.10 | 255.255.255.0 | 192.168.20.1 |
| PC3 | 192.168.20.11 | 255.255.255.0 | 192.168.20.1 |

## Switch Configuration (CS-SW)

```
enable
configure terminal
hostname CS-SW

vlan 10
 name SALES
vlan 20
 name HR
exit

interface range fastEthernet0/1 - 2
 switchport mode access
 switchport access vlan 10
exit

interface range fastEthernet0/3 - 4
 switchport mode access
 switchport access vlan 20
exit

interface gigabitEthernet0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20
exit

end
write memory
```

## Router Configuration (CSR)

```
enable
configure terminal
hostname CSR

interface gigabitEthernet0/0/0
 no shutdown
exit

interface gigabitEthernet0/0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
exit

interface gigabitEthernet0/0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
exit

end
write memory
```


## Notes

- This design uses a single physical link between the switch and router (802.1Q trunk) with sub-interfaces on the router — commonly known as **router-on-a-stick**.

