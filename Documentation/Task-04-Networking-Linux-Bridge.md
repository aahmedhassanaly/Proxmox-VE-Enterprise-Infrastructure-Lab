# Task 04 – Networking & Linux Bridge

## Objective

Understand how Proxmox uses Linux Bridges to connect virtual machines.

In this task, a new Linux Bridge was created for the lab without changing the existing `vmbr0` configuration.

---

## 1. Existing Network

The original Proxmox network was:

- Bridge: `vmbr0`
- IP: `192.168.100.1/24`
- Bridge ports: None

The existing `vmbr0` was not changed because it is the main network of the Proxmox host.

---

## 2. Physical Network Interface

The Proxmox host has the following network interface:

    ens4

The interface was checked with:

    ip -br link

Output showed:

    lo      UNKNOWN
    ens4    UP
    vmbr0   UNKNOWN

---

## 3. Create a New Linux Bridge

A new Linux Bridge was created from:

    Node → System → Network → Create → Linux Bridge

Configuration:

    Name: vmbr1
    IPv4/CIDR: 192.168.200.1/24
    Gateway: None
    Bridge ports: None
    Autostart: Yes

The new bridge was created specifically for the lab.
<img width="1919" height="725" alt="image" src="https://github.com/user-attachments/assets/617245d3-9fcc-49d8-9032-d333c4a3018a" />

---

## 4. Verify vmbr1

The bridge was verified from the Proxmox Shell:

    ip addr show vmbr1

Important configuration:

    vmbr1
    IPv4: 192.168.200.1/24
    State: UP

This confirmed that `vmbr1` was active.

---

## 5. Connect Windows VM

The Windows test VM network device was changed to:

    Bridge: vmbr1

A static IP was configured inside Windows:

    IP Address: 192.168.200.10
    Subnet Mask: 255.255.255.0
    Gateway: None
    DNS: None

The Windows VM successfully reached the Proxmox bridge:

    ping 192.168.200.1

Result:

    Successful

---


## 6. Connect windows2 VM

The windows2 VM was also connected to:

    Bridge: vmbr1

Static IP configuration:

    IP Address: 192.168.200.20/24
    Gateway: None
    DNS: None

The configuration was applied using Netplan.

The windows2 VM successfully reached the Windows VM:

    ping 192.168.200.10

Result:

    Successful

---

## 7. VM-to-VM Communication

The final topology was:

    Proxmox
       |
      vmbr1
    192.168.200.1/24
       |
       +-------------------+
       |                   |
    Windows              windows2
    .200.10              .200.20

Tests performed:

    Windows → vmbr1       SUCCESS
    windows2 → vmbr1        SUCCESS
    windows → windows2      SUCCESS
    windows2 → Windows      SUCCESS

---<img width="1919" height="792" alt="image" src="https://github.com/user-attachments/assets/c155a8de-1fbe-4f1f-9681-b3c0634964c2" />

<img width="1625" height="1029" alt="image" src="https://github.com/user-attachments/assets/a3e473d3-ae97-4670-bfe3-a1cfce01f1dc" />

## 8. Bridge Verification

The Proxmox host was checked using:

    bridge link

The output showed the network interface of the Windows VM connected through the Proxmox firewall bridge to `vmbr1`.

This confirmed that the VM network interface was forwarding traffic through the Linux Bridge.

---

## 9. Important Concepts

### Linux Bridge

A Linux Bridge works like a virtual Layer 2 switch.

Example:

    VM1 ─┐
         |
    VM2 ─┼── vmbr1
         |
    VM3 ─┘

VMs connected to the same bridge can communicate with each other when their IP configuration allows it.

### vmbr0 vs vmbr1

    vmbr0
    Main existing Proxmox network

    vmbr1
    New isolated lab network

`vmbr0` was not modified during this task.

---

## 10. Result

Task 4 was completed successfully.

### Completed

- [x] Understand Linux Bridge
- [x] Create `vmbr1`
- [x] Configure `192.168.200.1/24`
- [x] Connect Windows VM
- [x] Connect windows2 VM
- [x] Configure static IP addresses
- [x] Test VM-to-Bridge communication
- [x] Test VM-to-VM communication
- [x] Verify bridge forwarding
- [x] Keep the original `vmbr0` unchanged

## Task Status

**Task 04 – Networking & Linux Bridge: COMPLETED**
