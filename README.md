# Proxmox VE Enterprise Infrastructure Lab

An enterprise-style hands-on lab for learning and practicing Proxmox VE administration.

The project covers virtualization, storage, networking, clustering, high availability, replication, permissions, monitoring, maintenance, Ceph, Proxmox Backup Server, LXC containers, and Veeam Backup & Replication.

## Lab Goals

This project is built around practical Proxmox administration tasks that are common in enterprise environments.

The main goals are to:

- Build and manage Proxmox virtual machines.
- Work with different storage types.
- Configure Linux bridges and VLANs.
- Manage Proxmox clusters and VM migration.
- Configure High Availability and replication.
- Manage users, groups, and permissions.
- Monitor Proxmox nodes and virtual machines.
- Perform updates and planned maintenance.
- Work with Ceph and shared storage concepts.
- Deploy Proxmox Backup Server.
- Manage LXC containers.
- Integrate Veeam Backup & Replication with Proxmox.
- Test backup and restore operations.

## Lab Environment

The main lab is built as a nested virtualization environment in Google Cloud.

### Lab Topology

The following diagram shows the main lab topology used across the project. It includes the Google Cloud network, the two-node Proxmox cluster, internal Proxmox bridges, Proxmox Backup Server, and the Veeam Proxmox Worker.

![Proxmox Lab Topology](./Documentation/proxmox-lab-topology.svg)

### Proxmox Nodes

| Node | Role |
|---|---|
| proxmox-lab | Main Proxmox node |
| proxmox-node2 | Second Proxmox cluster node |

### Main Network

The Proxmox management network uses the Google Cloud network.

The lab also contains internal Proxmox bridges for VM and testing workloads.

The main Proxmox network was kept separate from the isolated lab networking used for many exercises.

### Main Storage

The lab uses several Proxmox storage technologies during different tasks:

- Directory storage
- LVM
- ZFS
- Ceph RBD
- Proxmox Backup Server storage
- Veeam Backup Repository

## Documentation

The Documentation directory contains the complete task-by-task lab record.

### Task 1 — Proxmox Foundation

Host assessment, CPU and RAM review, disk assessment, network review, Linux bridge creation, and the first test VM.

[Task 1](./Documentation/01-proxmox-foundation-assessment.md)

### Task 2 — Storage Engineering

Directory storage, LVM, ZFS, virtual disks, detach and re-attach testing, and snapshot rollback.

[Task 2](./Documentation/02-storage-engineering.md)

### Task 3 — VM, Template, Clone and Snapshot Management

Windows VM creation, VirtIO, QEMU Guest Agent, snapshots, templates, Full Clone, and rollback testing.

[Task 3](./Documentation/03-VM-Template-Clone-Snapshot.md)

### Task 4 — Networking & Linux Bridge

Creation and testing of the isolated `vmbr1` Linux bridge.

[Task 4](./Documentation/Task-04-Networking-Linux-Bridge.md)

### Task 5 — VLAN & Network Configuration

VLAN-aware bridge configuration, VLAN 10 and VLAN 20, VM VLAN tags, and inter-VLAN routing.

[Task 5](./Documentation/Task-05-VLAN-Network-Configuration.md)

### Task 6 — Proxmox Backup & Restore

VM backup, backup storage, restore to a new VM, and restore verification.

[Task 6](./Documentation/Task-06-Proxmox-Backup-Restore.md)

### Task 7 — VM Migration

Offline migration, Live Migration, storage migration concepts, and migration troubleshooting.

[Task 7](./Documentation/Task-07-VM-Migration.md)

### Task 8 — Proxmox High Availability

HA resources, node maintenance, HA settings, and VM failover behavior.

[Task 8](./Documentation/Task-08-Proxmox-HA.md)

### Task 9 — Cluster Management

Proxmox cluster configuration, cluster options, logs, and cluster administration.

[Task 9](./Documentation/Task-09-Cluster-Management.md)

### Task 10 — Proxmox Replication

VM replication between Proxmox nodes and replication status verification.

[Task 10](./Documentation/Task-10-Proxmox-Replication.md)

### Task 11 — User & Permission Management

Users, groups, realms, roles, and VM-level permissions.

[Task 11](./Documentation/Task-11-User-Permission-Management.md)

### Task 12 — Monitoring & Performance

Node and VM monitoring, CPU, memory, load, and performance checks.

[Task 12](./Documentation/Task-12-Monitoring-Performance.md)

### Task 13 — Updates & Maintenance

Repository management, updates, Maintenance Mode, kernel updates, reboot, and post-maintenance verification.

[Task 13](./Documentation/Task-13-Proxmox-Updates-Maintenance.md)

### Task 14 — Advanced VM Administration

CPU and memory administration, disk management, boot settings, QEMU Guest Agent, UEFI, TPM, Protection, Firewall, Monitor, and VM logs.

[Task 14](./Documentation/Task-14-Advanced-VM-Administration.md)

### Task 15 — Proxmox Ceph Storage

Ceph MON, MGR, OSD, pools, PGs, CRUSH, RBD storage, and VM storage on Ceph.

[Task 15](./Documentation/Task-15-Proxmox%20Ceph%20Storage.md)

### Task 16 — Proxmox Backup Server

PBS datastore, backup, incremental data, deduplication, verification, restore, prune, retention, and garbage collection.

[Task 16](./Documentation/Task-16-Proxmox-Backup-Server.md)

### Task 17 — LXC Container Administration

LXC creation, unprivileged containers, CPU and memory limits, console access, configuration, cloning concepts, and backup/restore administration.

[Task 17](./Documentation/Task-17-LXC-Container-Administration.md)

### Task 18 — Veeam Backup & Replication

Veeam integration with Proxmox, Proxmox Worker deployment, backup jobs, Veeam Repository, HotAdd processing, full VM restore, and successful VM boot verification.

[Task 18](./Documentation/Task-18-Veeam-Backup-Replication.md)

## Key Technologies

- Proxmox VE
- QEMU/KVM
- LXC
- Linux Bridge
- VLAN
- ZFS
- LVM
- Ceph
- Proxmox Backup Server
- Veeam Backup & Replication
- Google Cloud

## Enterprise Scenarios

The lab focuses on realistic administration work such as:

- Creating and maintaining VMs.
- Moving workloads between hosts.
- Planned host maintenance.
- HA resource management.
- VM replication.
- Storage management.
- Backup and recovery.
- Monitoring and troubleshooting.
- User access control.
- Container administration.
- Disaster recovery testing.

## Important Lab Notes

This is a learning environment, not a production deployment.

Some configurations are simplified to fit the lab environment. For example, the Ceph work uses a two-node design with different OSD capacities. Production deployments require more planning around node count, storage balance, failure domains, networking, and recovery design.

The lab also uses isolated Proxmox networks for safe testing so that the main management network is not changed unnecessarily.

## What This Project Demonstrates

This project demonstrates practical work across the main areas of Proxmox VE administration:

```
Virtualization
     |
     +-- VM Management
     +-- Templates & Clones
     +-- LXC
     |
Storage
     |
     +-- Directory
     +-- LVM
     +-- ZFS
     +-- Ceph
     |
Cluster
     |
     +-- Migration
     +-- HA
     +-- Replication
     +-- Permissions
     |
Backup & Recovery
     |
     +-- Proxmox Backup
     +-- Proxmox Backup Server
     +-- Veeam Backup & Replication
     |
Operations
     |
     +-- Monitoring
     +-- Updates
     +-- Maintenance
     +-- Troubleshooting
```

## Final Outcome

The project is designed to show a complete Proxmox administration workflow from basic host setup to advanced storage, cluster management, backup, recovery, containers, and external backup integration.

Each task is documented with the configuration, practical steps, verification, troubleshooting notes, and the final result.
