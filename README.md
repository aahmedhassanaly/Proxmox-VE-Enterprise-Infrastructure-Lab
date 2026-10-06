# Proxmox VE Enterprise Infrastructure Lab

**Portfolio Priority: #3 — Virtualization & Infrastructure Operations**

An enterprise-style hands-on lab for **Proxmox VE administration**, covering virtualization, storage, networking, clustering, high availability, replication, permissions, monitoring, maintenance, Ceph, Proxmox Backup Server, LXC, and Veeam integration.

## Lab Architecture

The main lab is built as nested virtualization in Google Cloud.

<img width="1536" height="1024" alt="Proxmox enterprise lab topology" src="https://github.com/user-attachments/assets/c671e9ae-c4a7-45c6-8eac-333b6c74ca0d" />

### Proxmox Nodes

| Node | Role |
|---|---|
| `proxmox-lab` | Main Proxmox node |
| `proxmox-node2` | Second cluster node |

The lab uses the Google Cloud network for management and isolated Proxmox bridges for safe workload testing.

### Storage Technologies

- Directory storage
- LVM
- ZFS
- Ceph RBD
- Proxmox Backup Server
- Veeam Backup Repository

## Tasks

| # | Task | Status |
|---:|---|:---:|
| 01 | [Proxmox Foundation Assessment](Documentation/01-proxmox-foundation-assessment.md) | ✅ |
| 02 | [Storage Engineering](Documentation/02-storage-engineering.md) | ✅ |
| 03 | [VM, Template, Clone & Snapshot Management](Documentation/03-VM-Template-Clone-Snapshot.md) | ✅ |
| 04 | [Linux Bridge Networking](Documentation/Task-04-Networking-Linux-Bridge.md) | ✅ |
| 05 | [VLAN Network Configuration](Documentation/Task-05-VLAN-Network-Configuration.md) | ✅ |
| 06 | [Proxmox Backup & Restore](Documentation/Task-06-Proxmox-Backup-Restore.md) | ✅ |
| 07 | [VM Migration](Documentation/Task-07-VM-Migration.md) | ✅ |
| 08 | [High Availability](Documentation/Task-08-Proxmox-HA.md) | ✅ |
| 09 | [Cluster Management](Documentation/Task-09-Cluster-Management.md) | ✅ |
| 10 | [VM Replication](Documentation/Task-10-Proxmox-Replication.md) | ✅ |
| 11 | [User & Permission Management](Documentation/Task-11-User-Permission-Management.md) | ✅ |
| 12 | [Monitoring & Performance](Documentation/Task-12-Monitoring-Performance.md) | ✅ |
| 13 | [Updates & Maintenance](Documentation/Task-13-Proxmox-Updates-Maintenance.md) | ✅ |
| 14 | [Advanced VM Administration](Documentation/Task-14-Advanced-VM-Administration.md) | ✅ |
| 15 | [Ceph Storage](Documentation/Task-15-Proxmox-Ceph-Storage.md) | ✅ |
| 16 | [Proxmox Backup Server](Documentation/Task-16-Proxmox-Backup-Server.md) | ✅ |
| 17 | [LXC Container Administration](Documentation/Task-17-LXC-Container-Administration.md) | ✅ |
| 18 | [Veeam Backup & Replication](Documentation/Task-18-Veeam-Backup-Replication.md) | ✅ |

Detailed implementation notes: [Documentation](Documentation/).

## Core Skills Demonstrated

### Virtualization
- QEMU/KVM
- VM lifecycle management
- Templates, clones, snapshots
- LXC containers

### Storage
- Directory storage
- LVM
- ZFS
- Ceph / RBD
- Proxmox Backup Server

### Networking
- Linux bridges
- VLAN-aware networking
- VM VLAN tagging
- Isolated test networks

### Cluster Operations
- Cluster administration
- Live/offline migration
- High Availability
- VM replication
- Planned maintenance

### Operations & Security
- User/group/role permissions
- Monitoring
- Updates and maintenance
- Troubleshooting
- Backup and recovery
- Veeam integration

## Enterprise Scenarios

The lab emphasizes operational tasks such as:

- Moving workloads between hosts
- Planned host maintenance
- HA resource management
- Storage administration
- Backup and restore
- Monitoring and troubleshooting
- Access control
- Disaster recovery testing

## Important Lab Notes

This is a learning environment, not a production deployment.

The Ceph work uses a two-node design with different OSD capacities. Production Ceph requires careful planning around node count, storage balance, failure domains, networking, and recovery design.

## Documentation Structure

```text
Proxmox-VE-Enterprise-Infrastructure-Lab/
├── README.md
└── Documentation/
    ├── 01-proxmox-foundation-assessment.md
    ├── 02-storage-engineering.md
    ├── 03-VM-Template-Clone-Snapshot.md
    ├── Task-04-Networking-Linux-Bridge.md
    ├── Task-05-VLAN-Network-Configuration.md
    ├── Task-06-Proxmox-Backup-Restore.md
    ├── ...
    ├── Task-18-Veeam-Backup-Replication.md
    └── proxmox-lab-topology.svg
```

## Result

The project demonstrates a complete Proxmox administration workflow from host setup through virtualization, storage, networking, cluster operations, backup, recovery, containers, and external backup integration.
