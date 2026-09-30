# Task 14 — Advanced VM Administration

## Objective

Learn advanced day-to-day VM administration in Proxmox VE.

The focus was on managing VM resources, storage, boot behavior, guest integration, troubleshooting tools, and basic VM security.

## Lab Environment

- Proxmox VE Cluster: `proxmox-lab`
- Test VM: `ubuntu-test01`
- VM ID: `100`
- OS: Ubuntu Linux
- Storage: ZFS
- Network: Existing lab configuration

No changes were made to the main `vmbr0` network.

## CPU Administration

Reviewed the VM CPU configuration:

- Sockets: 1
- Cores: 1
- CPU Type: `x86-64-v2-AES`

Important concepts:

- Sockets define virtual CPU packages.
- Cores define the CPU cores available to the VM.
- CPU Type defines the CPU features exposed to the VM.

CPU configuration should be planned carefully in a cluster because CPU compatibility can affect VM migration.

## Memory Administration

Reviewed VM memory settings and Memory Ballooning.

Memory Ballooning allows the guest memory allocation to be adjusted dynamically when supported by the guest operating system and QEMU Guest Agent.

This can help improve memory utilization on a Proxmox node.

## Disk Administration

Reviewed the VM disk:

- Disk: `scsi0`
- Storage: `zfspool`

The disk resize feature was reviewed.

Important concept:

Increasing the virtual disk size in Proxmox does not automatically expand the partition and filesystem inside the guest operating system.

The general process is:

1. Increase the virtual disk size in Proxmox.
2. Detect the new disk size inside the guest.
3. Expand the partition if required.
4. Expand the filesystem.

## Boot Order

Reviewed:

**VM → Options → Boot Order**

The system disk should normally be configured as the primary boot device.

Boot order becomes important when troubleshooting boot problems or working with ISO images.

## QEMU Guest Agent

Reviewed and enabled the QEMU Guest Agent.

The Guest Agent allows Proxmox to communicate with the operating system running inside the VM.

It can provide useful guest information and support guest-aware operations.

Inside Linux, its service can be checked with:

    systemctl status qemu-guest-agent

## VM Options

Reviewed important VM options, including:

- Start at boot
- Boot Order
- QEMU Guest Agent
- ACPI
- Protection

Hardware settings mainly control the virtual hardware and resources, while Options control VM behavior.

## BIOS / UEFI

Reviewed the difference between:

- SeaBIOS
- OVMF / UEFI

UEFI is commonly used for modern operating systems and can be combined with additional virtual security components when required.

## TPM

TPM means Trusted Platform Module.

In Proxmox, a virtual TPM can be added to a VM.

TPM is mainly related to operating system security features such as:

- BitLocker
- Cryptographic key protection
- Modern Windows security requirements

TPM is different from the Proxmox Firewall.

## VM Protection

Reviewed:

**VM → Options → Protection**

Protection is designed to reduce the risk of accidental destructive administrative operations on an important VM.

It does not protect the VM from network attacks.

## VM Firewall

Reviewed the VM-level Firewall.

The Proxmox Firewall can control network traffic associated with the VM.

Typical examples include:

- Allow SSH
- Allow required application ports
- Allow ICMP
- Block unwanted traffic

Firewall is a network security feature.

It is different from:

- TPM, which provides virtual security hardware
- Protection, which helps prevent dangerous administrative actions

No blocking firewall rule was added to the production/lab VM during this task to avoid losing connectivity.

## VM Monitor

Reviewed the VM Monitor.

The Monitor provides a lower-level interface to the QEMU virtual machine.

It can be useful during advanced troubleshooting and VM administration.

Destructive commands were not executed during this task.

## VM Log

Reviewed the VM Log.

The log can help identify events such as:

- VM start
- VM shutdown
- Migration
- HA actions
- Other VM-related events

Logs are important when troubleshooting unexpected VM behavior.

## Security Comparison

    Firewall
        ↓
    Protects network traffic

    TPM
        ↓
    Provides virtual security hardware

    Protection
        ↓
    Helps prevent dangerous administrative operations

## Practical Scenario

A virtualization administrator manages an important Ubuntu VM.

The administrator needs to:

- Review CPU and memory allocation.
- Check the VM disk configuration.
- Verify the boot order.
- Confirm QEMU Guest Agent operation.
- Review VM logs during troubleshooting.
- Understand VM-level Firewall settings.
- Protect important VMs from accidental deletion.
- Understand when TPM is required.

These are common VM administration activities in a Proxmox environment.

## What I Learned

- How to review advanced VM CPU and memory settings.
- How Memory Ballooning works at a high level.
- How Proxmox virtual disk resizing works.
- Why guest partitions and filesystems may need additional expansion.
- How Boot Order affects VM startup.
- The purpose of QEMU Guest Agent.
- The difference between BIOS and UEFI.
- The purpose of virtual TPM.
- The purpose of VM Protection.
- The difference between Firewall, TPM, and Protection.
- How VM Monitor and VM Log can help with troubleshooting.

## Result

Task 14 was completed successfully.

The VM administration concepts were reviewed without making unnecessary changes to the main lab network.

The lab now includes practical knowledge of Proxmox VM administration beyond basic VM creation, snapshots, templates, migration, HA, replication, permissions, monitoring, and maintenance.
