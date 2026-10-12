# Enterprise Network Security with VLANs and ACLs

![Cisco](https://img.shields.io/badge/Network-Cisco%20Packet%20Tracer-blue)
![VLAN](https://img.shields.io/badge/Technology-VLANs-green)
![Routing](https://img.shields.io/badge/Routing-Inter--VLAN-orange)
![Security](https://img.shields.io/badge/Security-Extended%20ACLs-red)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)

## 📌 Project Overview

**Enterprise Network Security with VLANs and ACLs** is a network design and configuration project implemented using Cisco Packet Tracer.

The project demonstrates how to divide an enterprise network into separate departmental VLANs, enable communication between VLANs using router-on-a-stick, and enforce network security policies using extended Access Control Lists (ACLs).

The network contains four departments:

- Human Resources (HR)
- Finance
- Information Technology (IT)
- Guest

Each department operates in a separate IP subnet. ACLs control traffic between departments according to the organization's security requirements.

## 🎯 Objectives

- Design an enterprise network using Cisco Packet Tracer.
- Configure VLANs for departmental network segmentation.
- Assign switch ports to the correct VLANs.
- Configure 802.1Q trunking between a switch and router.
- Implement inter-VLAN routing using router subinterfaces.
- Configure extended ACLs to restrict unauthorized traffic.
- Verify connectivity and security policies.
- Practice Cisco IOS configuration and network troubleshooting.

## 🏗️ Network Topology

The project uses a Cisco 2911 router, a Cisco 2960 switch, and eight PCs.

```text
                    +----------------+
                    |   Router R1    |
                    | Cisco 2911     |
                    | G0/0           |
                    +-------+--------+
                            |
                      802.1Q Trunk
                            |
                    +-------+--------+
                    |    Switch SW1  |
                    | Cisco 2960     |
                    +-------+--------+
                            |
       +--------------------+--------------------+
       |                    |                    |
   VLAN 10              VLAN 20              VLAN 30
      HR                Finance                 IT
    PC1 PC2              PC3 PC4              PC5 PC6

                            |
                         VLAN 40
                          Guest
                         PC7 PC8
```

The diagram represents the logical VLAN grouping. All eight PCs connect physically to the same switch, and the router provides their default gateways.

## 🧰 Technologies and Devices

| Component | Details |
|---|---|
| Network simulator | Cisco Packet Tracer |
| Router | Cisco 2911 |
| Switch | Cisco 2960 |
| End devices | 8 PCs |
| Routing method | Router-on-a-stick |
| Trunking protocol | IEEE 802.1Q |
| Network segmentation | VLANs |
| Security | Extended IPv4 ACLs |
| Configuration language | Cisco IOS CLI |

## 🌐 VLAN and IP Addressing Plan

| VLAN ID | Department | Network | Default Gateway |
|---:|---|---|---|
| 10 | HR | `192.168.10.0/24` | `192.168.10.1` |
| 20 | Finance | `192.168.20.0/24` | `192.168.20.1` |
| 30 | IT | `192.168.30.0/24` | `192.168.30.1` |
| 40 | Guest | `192.168.40.0/24` | `192.168.40.1` |

**Subnet mask:** `255.255.255.0`

### PC Addressing

| PC | VLAN | IPv4 Address | Default Gateway |
|---|---:|---|---|
| PC1 | 10 | `192.168.10.10` | `192.168.10.1` |
| PC2 | 10 | `192.168.10.11` | `192.168.10.1` |
| PC3 | 20 | `192.168.20.10` | `192.168.20.1` |
| PC4 | 20 | `192.168.20.11` | `192.168.20.1` |
| PC5 | 30 | `192.168.30.10` | `192.168.30.1` |
| PC6 | 30 | `192.168.30.11` | `192.168.30.1` |
| PC7 | 40 | `192.168.40.10` | `192.168.40.1` |
| PC8 | 40 | `192.168.40.11` | `192.168.40.1` |

## ⚙️ Network Configuration

### 1. Creating VLANs

The following commands create the four departmental VLANs on SW1.

```cisco
enable
configure terminal

vlan 10
 name HR
exit

vlan 20
 name FINANCE
exit

vlan 30
 name IT
exit

vlan 40
 name GUEST
exit

end
show vlan brief
```

### 2. Assigning Switch Ports

The access ports are assigned according to the departmental VLAN plan.

```cisco
configure terminal

interface range fastEthernet 0/1-2
 switchport mode access
 switchport access vlan 10
exit

interface range fastEthernet 0/3-4
 switchport mode access
 switchport access vlan 20
exit

interface range fastEthernet 0/5-6
 switchport mode access
 switchport access vlan 30
exit

interface range fastEthernet 0/7-8
 switchport mode access
 switchport access vlan 40
exit

end
show vlan brief
```

| Switch ports | VLAN | Department |
|---|---:|---|
| Fa0/1–Fa0/2 | 10 | HR |
| Fa0/3–Fa0/4 | 20 | Finance |
| Fa0/5–Fa0/6 | 30 | IT |
| Fa0/7–Fa0/8 | 40 | Guest |
| Fa0/24 | Trunk | Router connection |

### 3. Configuring the Trunk Link

SW1 uses FastEthernet0/24 to connect to router interface GigabitEthernet0/0.

```cisco
configure terminal
interface fastEthernet 0/24
 switchport mode trunk
 no shutdown
end
show interfaces trunk
```

The trunk carries tagged traffic for VLANs 10, 20, 30, and 40.

### 4. Configuring Inter-VLAN Routing

Router-on-a-stick allows a single physical router interface to provide gateways for multiple VLANs through logical subinterfaces.

```cisco
enable
configure terminal

interface gigabitEthernet 0/0
 no shutdown
exit

interface gigabitEthernet 0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
exit

interface gigabitEthernet 0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
exit

interface gigabitEthernet 0/0.30
 encapsulation dot1Q 30
 ip address 192.168.30.1 255.255.255.0
exit

interface gigabitEthernet 0/0.40
 encapsulation dot1Q 40
 ip address 192.168.40.1 255.255.255.0
exit

end
show ip interface brief
```

Each PC must be configured with the correct IPv4 address, subnet mask, and default gateway.

## 🔐 Access Control List Configuration

Extended ACLs restrict traffic between departmental networks.

### Security Policies

| Source | Destination | Policy |
|---|---|---|
| HR | Finance | Block |
| Finance | HR | Block |
| IT | All departments | Allow, subject to other rules |
| Guest | HR | Block |
| Guest | Finance | Block |
| Guest | IT | Block |

### 1. HR Access Control

```cisco
configure terminal

ip access-list extended HR-IN
 deny ip 192.168.10.0 0.0.0.255 192.168.20.0 0.0.0.255
 permit ip any any
exit

interface gigabitEthernet 0/0.10
 ip access-group HR-IN in
exit
```

### 2. Finance Access Control

```cisco
ip access-list extended FINANCE-IN
 deny ip 192.168.20.0 0.0.0.255 192.168.10.0 0.0.0.255
 permit ip any any
exit

interface gigabitEthernet 0/0.20
 ip access-group FINANCE-IN in
exit
```

### 3. Guest Access Control

```cisco
ip access-list extended GUEST-IN
 deny ip 192.168.40.0 0.0.0.255 192.168.10.0 0.0.0.255
 deny ip 192.168.40.0 0.0.0.255 192.168.20.0 0.0.0.255
 deny ip 192.168.40.0 0.0.0.255 192.168.30.0 0.0.0.255
 permit ip any any
exit

interface gigabitEthernet 0/0.40
 ip access-group GUEST-IN in
exit

end
```

**Security note:** These ACLs filter packets entering the router from their respective VLANs. They block the specified source-to-destination traffic; they do not automatically block every possible reverse-direction or session-related flow. The IT VLAN has no restrictive ACL applied in this configuration.

## 🧪 Verification and Testing

Use the following commands to validate the configuration.

### Switch verification

```cisco
show vlan brief
show interfaces trunk
show running-config
```

### Router verification

```cisco
show ip interface brief
show ip route
show access-lists
show running-config
```

### Connectivity tests

Run `ping` from the relevant PC's Command Prompt.

| Test | Expected result |
|---|---|
| PC1 → `192.168.10.1` | Success |
| PC1 → PC2 | Success |
| PC1 → PC3 | Blocked |
| PC3 → PC1 | Blocked |
| PC5 → PC3 | Success |
| PC7 → PC1 | Blocked |
| PC7 → PC3 | Blocked |
| PC7 → PC5 | Blocked |

A failed ping alone does not always prove an ACL is working. Verify ACL hit counters with `show access-lists` and confirm that permitted traffic succeeds.

## 📊 Results

The completed lab demonstrates:

- Successful creation of four departmental VLANs.
- Correct assignment of access ports to VLANs.
- Operational 802.1Q trunking.
- Router subinterfaces configured as departmental gateways.
- Extended ACL rules applied to selected VLAN interfaces.
- Connectivity tests for allowed and restricted traffic.

The lab was tested in Cisco Packet Tracer, and the reported connectivity and security tests behaved as expected.

## 📂 Project Files

```text
Enterprise-Network-Security/
│
├── README.md
├── Enterprise-Network-Security.pkt
├── configs/
│   ├── R1-config.txt
│   └── SW1-config.txt
└── screenshots/
    ├── topology.png
    ├── vlan-configuration.png
    ├── trunk-configuration.png
    ├── router-interfaces.png
    └── acl-testing.png
```

This is a suggested repository structure. Include only files and screenshots you have actually created.

## 📚 Skills Demonstrated

- Cisco IOS CLI configuration.
- VLAN creation and segmentation.
- Access port configuration.
- 802.1Q trunking.
- Router-on-a-stick inter-VLAN routing.
- IPv4 addressing and subnetting.
- Extended ACL configuration.
- Network access control and troubleshooting.
- Connectivity verification using ping and show commands.
- Cisco Packet Tracer network simulation.

## 🚀 Future Improvements

- Implement DHCP pools for each VLAN.
- Add a dedicated server VLAN.
- Introduce a management VLAN and secure device administration.
- Configure switch port security.
- Add redundant routers and switches.
- Implement HSRP for gateway redundancy.
- Add a documented firewall policy and additional security tests.

## 👨‍💻 Author

**Andrews GnanaSelvin D**

- GitHub: [Andrewsdavid2005](https://github.com/Andrewsdavid2005)
- LinkedIn: [Andrews GnanaSelvin D](https://www.linkedin.com/in/andrews-gnana-selvin-d)

## 📄 License

This project is available for educational and portfolio use. If you want to license it under the MIT License, add a `LICENSE` file containing the official MIT License text.

---

⭐ If this project helps you learn VLANs, routing, and network security, feel free to explore the configuration and suggest improvements.
