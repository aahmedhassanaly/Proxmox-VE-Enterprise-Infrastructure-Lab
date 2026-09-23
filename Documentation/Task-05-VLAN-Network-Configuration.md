# Task 05 - VLAN & Network Configuration

## Objective

The goal of this task was to configure VLANs on Proxmox and test communication between different VLANs.

The lab was built on the isolated `vmbr1` bridge to avoid changing the main `vmbr0` network.

In this task, I practiced:

- VLAN-aware Linux Bridge
- VLAN 10
- VLAN 20
- VM VLAN Tags
- Static IP configuration
- VLAN isolation
- Inter-VLAN Routing
- Access Port and Trunk Port concepts
- Basic VLAN troubleshooting

---

## Lab Environment

The main network was not changed.

The lab used:

    vmbr1
    192.168.200.1/24

VLAN networks:

    VLAN 10
    Network: 192.168.10.0/24
    Gateway: 192.168.10.1

    VLAN 20
    Network: 192.168.20.0/24
    Gateway: 192.168.20.1

VMs:

    Windows 1
    VLAN: 10
    IP: 192.168.10.20

    Windows 2
    VLAN: 20
    IP: 192.168.20.10

---

# Step 1 - Enable VLAN Aware

I opened:

    Node → System → Network

Then selected:

    vmbr1 → Edit

I enabled:

    VLAN aware = Yes

The existing `vmbr0` bridge was not modified.

---

# Step 2 - Create VLAN 10

I created a Linux VLAN from:

    Node → System → Network → Create → Linux VLAN

Configuration:

    Name: vmbr1.10
    VLAN ID: 10
    Raw Device: vmbr1
    IPv4/CIDR: 192.168.10.1/24
    Gateway: Empty
    Autostart: Yes

The VLAN interface was successfully created.
<img width="1555" height="597" alt="image" src="https://github.com/user-attachments/assets/382c634e-33a7-40ba-bde9-fbaefac10930" />

---

# Step 3 - Configure Windows 1

The network device of Windows 1 was configured with:

    Bridge: vmbr1
    VLAN Tag: 10

Windows 1 was configured with:

    IP Address: 192.168.10.20
    Subnet Mask: 255.255.255.0
    Default Gateway: 192.168.10.1
    DNS: Empty

Connectivity was tested with:

    ping 192.168.10.1

Result:

    Successful

This confirmed that Windows 1 could communicate with the VLAN 10 gateway.
<img width="1452" height="919" alt="image" src="https://github.com/user-attachments/assets/5a555c05-d60f-482f-9e61-67691f07619d" />

---

# Step 4 - Create VLAN 20

I created another Linux VLAN from:

    Node → System → Network → Create → Linux VLAN

Configuration:

    Name: vmbr1.20
    VLAN ID: 20
    Raw Device: vmbr1
    IPv4/CIDR: 192.168.20.1/24
    Gateway: Empty
    Autostart: Yes

The VLAN 20 interface was successfully created.

---
<img width="1537" height="917" alt="image" src="https://github.com/user-attachments/assets/a62ad4a0-695b-4098-8c99-f3bda10c3c10" />

# Step 5 - Configure Windows 2

The network device of Windows 2 was configured with:

    Bridge: vmbr1
    VLAN Tag: 20

Windows 2 was configured with:

    IP Address: 192.168.20.10
    Subnet Mask: 255.255.255.0
    Default Gateway: 192.168.20.1
    DNS: Empty

Connectivity was tested with:

    ping 192.168.20.1

Result:

    Successful

This confirmed that Windows 2 could communicate with the VLAN 20 gateway.

---

# Step 6 - Test VLAN Isolation

Before enabling routing, VLAN 10 and VLAN 20 were separate networks.

Windows 1:

    192.168.10.20

Windows 2:

    192.168.20.10

A device in VLAN 10 could not directly communicate with a device in VLAN 20.

This is expected because the two VLANs are different Layer 2 networks.

A Layer 3 router is required for communication between them.

---

# Step 7 - Configure Inter-VLAN Routing

Proxmox was used as the Layer 3 router for this lab.

IPv4 forwarding was enabled on the Proxmox host.

The configuration was verified with:

    sysctl net.ipv4.ip_forward

The result was:

    net.ipv4.ip_forward = 1

The routing table contained:

    192.168.10.0/24 dev vmbr1.10
    192.168.20.0/24 dev vmbr1.20

This allowed Proxmox to route traffic between VLAN 10 and VLAN 20.

---

# Step 8 - Windows Firewall Configuration

Both Windows VMs were using the Public network profile.

The Windows Firewall was not completely disabled.

Instead, the ICMP Echo Request rule was enabled from:

    Windows Defender Firewall with Advanced Security
    → Inbound Rules
    → File and Printer Sharing (Echo Request - ICMPv4-In)

The rule was enabled on both Windows VMs.

This allowed the ping tests required for the lab.
<img width="1423" height="948" alt="image" src="https://github.com/user-attachments/assets/2355ab5f-86d3-45fc-be1f-b1c8aacc2081" />

---

# Step 9 - Test Inter-VLAN Routing

From Windows 1:

    ping 192.168.20.10

Result:

    Successful
<img width="1458" height="932" alt="image" src="https://github.com/user-attachments/assets/3c48379a-8b97-453e-b72f-3582170c2d4b" />

From Windows 2:

    ping 192.168.10.20

Result:

    Successful
<img width="1507" height="915" alt="image" src="https://github.com/user-attachments/assets/1cdcd6dd-2761-4c07-a33a-6466b1ba22a6" />

This confirmed that Inter-VLAN Routing was working correctly.

---

# Step 10 - Access Port

An Access Port normally carries traffic for one VLAN.

Example:

    PC
      |
      | Access Port
      |
    Switch
      |
    VLAN 10

The endpoint normally sends untagged traffic.

The switch assigns the traffic to the configured VLAN.

Access Ports are commonly used for:

- PCs
- Printers
- Cameras
- Other end devices

---

# Step 11 - Trunk Port

A Trunk Port can carry multiple VLANs through the same link.

Example:

    Switch
       |
       | Trunk
       |
    Proxmox
       |
       +---- VLAN 10
       |
       +---- VLAN 20

Trunk connections are commonly used between:

- Switch and Switch
- Switch and Hypervisor
- Switch and Router
- Switch and Firewall

In a Proxmox environment, a VLAN-aware bridge can allow different VMs to use different VLAN Tags through the same physical connection.

---

# Step 12 - Final Topology

The final lab topology was:

    Proxmox
       |
     vmbr1
    VLAN Aware
       |
       +----------------------+
       |                      |
    VLAN 10                VLAN 20
       |                      |
    Windows 1              Windows 2
    192.168.10.20          192.168.20.10
       |                      |
    Gateway                Gateway
    192.168.10.1           192.168.20.1
       \                      /
        \                    /
         +-- Inter-VLAN Routing --+

---

# Verification

The following tests were completed successfully:

    VLAN 10 Gateway
    192.168.10.20 → 192.168.10.1

    VLAN 20 Gateway
    192.168.20.10 → 192.168.20.1

    Inter-VLAN Routing
    192.168.10.20 → 192.168.20.10

    Inter-VLAN Routing
    192.168.20.10 → 192.168.10.20

All tests were successful.

---

# What I Learned

In this task, I learned:

1. How to enable VLAN-aware mode on a Proxmox Linux Bridge.
2. How to create VLAN interfaces.
3. How to assign VLAN Tags to VMs.
4. How VLANs separate Layer 2 networks.
5. Why different VLANs cannot communicate without routing.
6. How Proxmox can perform Layer 3 routing in a lab.
7. The difference between Access Ports and Trunk Ports.
8. How Windows Firewall can affect ICMP testing.
9. How to troubleshoot VLAN connectivity step by step.

---

# Result

Task 5 was completed successfully.

The lab now has two working VLANs with successful Inter-VLAN Routing.

The main `vmbr0` network was not modified.

Status:

    Task 05 - VLAN & Network Configuration
    COMPLETED
