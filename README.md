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

## Task Roadmap

| Task | Area | Result |
|---|---|---|
| 01 | Proxmox foundation | ✅ |
| 02 | Storage engineering | ✅ |
| 03 | VM / template / clone / snapshot management | ✅ |
| 04 | Linux bridge networking | ✅ |
| 05 | VLAN networking | ✅ |
| 06 | Proxmox backup & restore | ✅ |
| 07 | VM migration | ✅ |
| 08 | High Availability | ✅ |
| 09 | Cluster management | ✅ |
| 10 | VM replication | ✅ |
| 11 | Users & permissions | ✅ |
| 12 | Monitoring & performance | ✅ |
| 13 | Updates & maintenance | ✅ |
| 14 | Advanced VM administration | ✅ |
| 15 | Ceph storage | ✅ |
| 16 | Proxmox Backup Server | ✅ |
| 17 | LXC administration | ✅ |
| 18 | Veeam Backup & Replication | ✅ |

Detailed implementation notes are available in [Documentation](Documentation/).

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
