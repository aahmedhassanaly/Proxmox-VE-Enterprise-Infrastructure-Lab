# Task 15 – Proxmox Ceph Storage

## Objective

The goal of this task is to build a Ceph storage cluster in Proxmox and use it as shared storage for virtual machine disks.

In this lab, Ceph was configured on two Proxmox nodes.

> Note: This is a learning lab with two nodes. Production Ceph deployments normally use more nodes and a more balanced storage design.

## Lab Environment

### Proxmox Nodes

- `proxmox-lab`
  - IP: `10.10.20.12`
  - 8 vCPU
  - 64 GB RAM
  - Ceph OSD: 350 GB

- `proxmox-node2`
  - IP: `10.10.20.14`
  - 4 vCPU
  - 16 GB RAM
  - Ceph OSD: 50 GB

### Ceph Network

The existing reachable Proxmox node network was used:

- Network: `10.10.20.0/24`

This network was selected because the two Proxmox nodes can communicate through it.

## Ceph Components

The Ceph cluster was configured with:

- 2 Monitor (MON) services
- 2 Manager (MGR) services
- 2 OSDs
- 1 Ceph Pool
- 1 RBD Storage

### Monitor

MON maintains Ceph cluster state, authentication information, and quorum.

The lab contains:

- `mon.proxmox-lab`
- `mon.proxmox-node2`

Both monitors were running and had quorum.

### Manager

MGR provides management and monitoring functions for the Ceph cluster.

The lab contains:

- `mgr.proxmox-lab`
- `mgr.proxmox-node2`

One Manager is active and the other is standby.
<img width="1919" height="809" alt="image" src="https://github.com/user-attachments/assets/d9dacf99-3d1d-4f96-8a41-8c2f1bcac55b" />

### OSD

OSD stands for Object Storage Daemon.

OSDs provide the actual storage capacity used by Ceph.

The final configuration was:

- `osd.0` → `proxmox-node2` → 50 GiB SSD
- `osd.1` → `proxmox-lab` → approximately 350 GiB HDD

Both OSDs were:

- `up`
- `in`
- BlueStore

## Creating the Ceph Pool

A pool named:

`ceph-vm`

was created for VM disks.

The main settings were:

- Name: `ceph-vm`
- Size: `2`
- Min Size: `2`
- Crush Rule: `replicated_rule`
- PG Autoscaler: `on`
- Add as Storage: Enabled
<img width="1919" height="714" alt="image" src="https://github.com/user-attachments/assets/5807e2d6-d553-42b8-94fb-e611aa6188da" />

### Pool Size

`Size = 2` means Ceph keeps two replicas of the data.

In this lab:

    VM data
       |
       +---- OSD.0 on proxmox-node2
       |
       +---- OSD.1 on proxmox-lab

This provides data redundancy between the two OSDs.

### Minimum Size

`Min Size = 2` defines the minimum number of replicas required for I/O.

This means the lab prioritizes data consistency and does not allow normal writes when only one replica is available.

### CRUSH Rule

The `replicated_rule` controls how Ceph distributes replicated data across the available OSDs.

It allows Ceph to select the OSD locations for the replicas.

### Placement Groups

The pool was created with PG Autoscaler enabled.

Placement Groups (PGs) are logical groups used by Ceph to organize and distribute objects across OSDs.

The Ceph cluster automatically distributed the PGs across the available OSDs.
<img width="1574" height="697" alt="image" src="https://github.com/user-attachments/assets/da6d639c-6bad-4b6b-90c7-5f2fa51a58fb" />

## RBD Storage

The pool was added as Proxmox storage.

The resulting storage configuration was:

- Name: `ceph-vm`
- Type: `RBD`
- Content: `Disk image, Container`
- Enabled: Yes
- Active: Yes

RBD stands for RADOS Block Device.

It provides block storage that Proxmox can use for VM disks.

## Moving a VM Disk to Ceph

A test VM disk was moved from local storage to:

`ceph-vm`

After the move, the VM disk was stored as an RBD volume in the Ceph pool.

The storage path changed conceptually from:

    Local/ZFS Storage
          |
          v
       VM Disk

to:

    Proxmox VM
          |
          v
    ceph-vm (RBD)
          |
          v
     Ceph Pool
          |
       +--+--+
       |     |
       v     v
     OSD.0 OSD.1
     Node2 Node1

## Verification

The Ceph OSD page showed:

- `osd.0` → `up / in`
- `osd.1` → `up / in`

The OSDs also showed Placement Groups assigned to them after the pool started being used.

The `ceph-vm` storage appeared on both Proxmox nodes as an active RBD storage.

The VM disk was successfully moved to the Ceph storage and the VM continued to operate normally.

## ZFS vs Replication vs Ceph

### ZFS

ZFS provides local storage on a Proxmox node.

    Node
      |
     ZFS
      |
    VM Disk

### Proxmox Replication

Replication keeps a copy of a VM disk on another Proxmox node.

    Node 1
      |
    VM Disk
      |
    Replication
      |
    Node 2
<img width="1919" height="764" alt="image" src="https://github.com/user-attachments/assets/5c189e4f-6af2-4d76-b06f-d9b14b95ead9" />

### Ceph

Ceph provides distributed shared storage across multiple OSDs.

    VM Disk
       |
      RBD
       |
    Ceph Pool
       |
    +--+--+
    |     |
   OSD   OSD
   Node1 Node2

## Important Production Note

This configuration is designed for learning.

The lab has only two Proxmox nodes and the OSD capacities are different:

- 50 GiB SSD
- approximately 350 GiB HDD

A production Ceph design normally requires careful planning of node count, OSD count, disk type, capacity, failure domains, network design, and performance.

## What I Learned

- Ceph provides distributed storage for Proxmox.
- MON provides cluster monitoring and quorum.
- MGR provides management functions.
- OSDs provide the actual storage.
- A Pool organizes Ceph storage logically.
- PGs are used to distribute objects across OSDs.
- CRUSH controls data placement.
- RBD provides block storage for VM disks.
- Ceph replication is different from Proxmox VM replication.
- A Ceph pool can be added to Proxmox as RBD storage.
- VM disks can run directly from Ceph storage.

## Result

Task 15 was completed successfully.

The Proxmox lab now has:

- 2 Ceph MONs
- 2 Ceph MGRs
- 2 Ceph OSDs
- 1 replicated Ceph Pool
- 1 RBD Proxmox Storage
- A VM disk successfully stored on Ceph

The complete storage path is:

    VM
     |
     v
    RBD
     |
     v
    Ceph Pool
     |
     +------ OSD.0
     |
     +------ OSD.1
