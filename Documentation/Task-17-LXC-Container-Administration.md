# Task 17 – LXC Container Administration

## Objective

Learn how to create and manage LXC containers in Proxmox.

The lab covers container resources, console access, CPU limits, container configuration, cloning, backup and restore.

## Lab Environment

- Platform: Proxmox VE 9
- Container ID: 120
- Hostname: app-lxc01
- OS: Debian GNU/Linux 13
- Container Type: Unprivileged LXC
- CPU: 1 Core
- Memory: 1024 MB
- Swap: 512 MB
- Root Disk: 8 GB
- Storage: local
- Network: Internal lab network on vmbr1

## Scenario

The goal is to deploy a lightweight Linux application container and practice common daily administration tasks used by a Proxmox Administrator.

The container does not require Internet access for this lab because the network is intentionally internal.

## Container Creation
Created an LXC container using the Debian 13 Standard template.

Configuration:

- CT ID: 120
- Hostname: app-lxc01
- Unprivileged: Enabled
- CPU: 1 Core
- RAM: 1 GB
- Swap: 512 MB
- Disk: 8 GB
- Storage: local

The container was successfully started and accessed through the Proxmox Console.

## LXC Kernel Concept

Inside the container, the following command was used:

    hostnamectl

The result showed:

    Virtualization: lxc
    Operating System: Debian GNU/Linux 13
    Kernel: 7.0.14-19-pve

This demonstrates an important difference between LXC and a virtual machine.

An LXC container does not have its own independent Linux kernel. It uses the Proxmox host kernel while providing process, filesystem and resource isolation.

## CPU Resource Management

The container was configured with:

- Cores: 1
- CPU Limit: 0.5

The following command was used inside the container:

    cat /proc/cpuinfo

The container was able to see CPU information provided by the host environment.

The CPU limit is controlled by Proxmox resource management and does not mean that the container receives a separate physical CPU.

After testing, the CPU Limit was returned to:

    0

This restores the normal CPU limit configuration.
<img width="1915" height="692" alt="image" src="https://github.com/user-attachments/assets/ad3651a3-46d1-4fc0-8113-5042fc8263b0" />

## Storage and Network Observation

The container was connected to the internal lab network.

Internet access was not required for this task.

An attempt to run:

    apt update

showed DNS/network resolution failures because the internal lab network does not provide Internet access.

This was not treated as a problem because Internet connectivity is outside the scope of this Proxmox administration task.

No changes were made to the main Proxmox management interface or primary network configuration.

## LXC Configuration

The container configuration can be viewed using:

    pct config 120

The configuration includes important settings such as:

- CPU
- Memory
- Swap
- Root filesystem
- Hostname
- Network interface
- Unprivileged mode

The configuration file is stored by Proxmox under:

    /etc/pve/lxc/120.conf

For normal administration, the Proxmox GUI or pct commands should be preferred instead of manually editing the configuration file.

## Container Console

The container was accessed from the Proxmox console.

It was also possible to enter the container from the Proxmox host using:

    pct enter 120

The container was successfully accessed as root.

## Snapshot Limitation

A Snapshot was attempted for CT 120.

Proxmox returned:

    The current guest configuration does not support taking new snapshots

This means that the current LXC storage/configuration does not support creating new snapshots.

The snapshot test was therefore not forced or worked around during this lab.

This is an important administration lesson: Snapshot support depends on the storage and volume configuration used by the guest.

No disk or storage changes were made just to enable snapshots.

## VM vs LXC

| Feature | VM | LXC |
|---|---|---|
| Guest Kernel | Independent | Shared with Proxmox host |
| Isolation | Strong | Process/filesystem/resource isolation |
| Resource Usage | Higher | Lower |
| Startup | Slower | Very fast |
| Windows Support | Yes | No |
| Linux Services | Yes | Very suitable |
| Typical Use | Windows/Linux servers | Lightweight Linux services |

## Real Enterprise Use Cases

LXC containers can be useful for lightweight Linux services such as:

- Nginx
- Monitoring tools
- DNS services
- Small internal applications
- Utility services
- Development environments

Virtual machines are normally preferred when a completely independent operating system kernel is required, such as Windows Server or certain enterprise applications.

## What I Learned

- How to create an LXC container.
- How to use an unprivileged container.
- How to manage CPU, RAM and Swap.
- How CPU limits work in Proxmox.
- How to access an LXC console.
- How to use pct commands.
- How to inspect LXC configuration.
- The difference between an LXC container and a VM.
- That Snapshot support depends on the underlying storage/configuration.
- How LXC containers can be cloned.
- How LXC containers can be backed up and restored.

## Result

Task 17 was completed successfully.

The Debian 13 LXC container `app-lxc01` was created and managed successfully.

Resource management, console access, configuration inspection, cloning concepts, and backup/restore administration were covered.

The Snapshot operation was tested but was not supported by the current guest storage/configuration, so no storage changes were made only to enable snapshots.

The main Proxmox network configuration was left unchanged.
