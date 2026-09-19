# Task 1 — Proxmox Foundation & Infrastructure Assessment

## Objective

The goal of this task was to understand the Proxmox VE host, check its resources, storage, networking, and deploy the first test VM.

The Proxmox server is running as a nested virtualization lab inside Google Cloud.

---

## 1. Host Assessment

The Proxmox node was checked from the Proxmox web interface.

- Node: `proxmox-lab`
- Proxmox VE: `8.4.21`
- Kernel: `6.8.12-43-pve`
- CPU: 10 vCPU
- RAM: 62.80 GiB
- Boot Mode: EFI

The node is currently running as a standalone Proxmox host.

---

## 2. Storage Assessment

The host contains the Proxmox system disk and an additional unused disk.

| Disk | Size | Purpose |
|---|---:|---|
| `/dev/sda` | ~54 GB | Proxmox system disk |
| `/dev/sdb` | ~537 GB | Reserved for future storage lab |

The Proxmox local storage is:

    /var/lib/vz

The additional `/dev/sdb` disk was intentionally left unused because it will be used in the Storage Engineering task.

---

## 3. Network Assessment

The main Proxmox management interface is:

    ens4

The current management configuration is:

- IP Address: `10.10.20.12/32`
- Gateway: `10.10.20.1`
- Network: `10.10.20.0/24`

The initial Proxmox configuration did not contain a VM bridge.

The network was inspected using:

    ip -br addr

and:

    ip route

The environment is running nested inside Google Cloud, so the network design is different from a normal physical Proxmox server.

---

## 4. Proxmox VM Bridge

A Linux bridge named `vmbr0` was created for the internal VM network.

Configuration:

| Setting | Value |
|---|---|
| Bridge | `vmbr0` |
| IP Address | `192.168.100.1/24` |
| Bridge Port | None |
| Autostart | Yes |
| VLAN Aware | No |

The bridge was verified as UP.

This bridge is currently used as an internal network for Proxmox virtual machines.

---

## 5. Test VM Deployment

A test Ubuntu Server virtual machine was created to verify the Proxmox VM lifecycle.

| Setting | Value |
|---|---|
| VM ID | `100` |
| Name | `ubuntu-test01` |
| OS | Ubuntu Server 24.04 |
| CPU | 2 Cores |
| RAM | 2048 MB |
| Disk | 20 GB |
| Network | `vmbr0` |
| NIC Model | VirtIO |

The VM was successfully created and started.

Ubuntu Server completed the boot process successfully.

---

## 6. Troubleshooting

The first VM start failed because the configured bridge did not exist.

Error:

    bridge 'vmbr0' does not exist

The problem was identified by checking the Proxmox network configuration.

The `vmbr0` bridge was then created and verified as UP.

After that, the VM was able to start successfully.

Inside Ubuntu, the virtual network interface was detected as:

    ens18

However, the guest did not automatically receive an IP address.

Complete guest networking, NAT, and VM connectivity testing will be handled later in:

**Task 4 — Proxmox Networking**

---

## 7. Skills Practiced

This task provided practical experience with:

- Proxmox VE host assessment
- CPU and RAM resource analysis
- Disk identification
- Proxmox storage inspection
- Network interface inspection
- Routing table inspection
- Linux bridge configuration
- VM creation
- VM hardware configuration
- Ubuntu Server deployment
- VirtIO network adapters
- Basic virtualization troubleshooting
- Nested virtualization concepts

---

## 8. Task Result

**Task 1 — Completed**

### Completed

- Proxmox host assessment
- CPU and RAM assessment
- Disk assessment
- Storage assessment
- Network assessment
- Linux bridge creation
- Test VM creation
- VM boot verification
- Basic troubleshooting

د

## 9. Key Infrastructure Lessons

This task demonstrated an important difference between a normal Proxmox installation and a nested cloud environment.

The architecture is:

    Google Cloud Network
            |
            v
       GCP VM Network
            |
            v
          ens4
            |
            v
       Proxmox VE
            |
            v
         vmbr0
            |
            v
      Proxmox Virtual Machines

The Proxmox host provides the virtualization layer, while the Google Cloud network provides the external network environment.

---
<img width="1906" height="870" alt="image" src="https://github.com/user-attachments/assets/993de50a-10ae-497c-a3f0-53aef90b6122" />

