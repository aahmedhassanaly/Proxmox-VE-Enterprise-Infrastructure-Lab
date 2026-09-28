# Task 10 - Proxmox Replication

## Objective

Learn how Proxmox Replication keeps a synchronized copy of a VM on another Proxmox node.

The lab focuses on ZFS storage, replication jobs, scheduled synchronization, and the relationship between Replication and HA.

## Lab Environment

Cluster:

- Cluster Name: proxmox-lab
- Node 1: proxmox-lab
- Node 2: proxmox-node2

VM:

- VM ID: 100
- VM Name: ubuntu-test01

Storage:

- ZFS Storage: zfspool
- Source Node: proxmox-lab
- Target Node: proxmox-node2

## ZFS Storage Preparation

Proxmox Replication requires ZFS-based storage.

A separate disk was added to the nodes and configured as ZFS storage.

The existing Proxmox OS disks were not modified.

The ZFS storage was already available in Proxmox as:

- Storage ID: zfspool
- Type: ZFS
- Content: Disk image, Container

The VM disk was moved from the Directory Storage to the ZFS Storage.

The VM configuration showed:

    scsi0: zfspool:vm-100-disk-0,iothread=1,size=32G

This confirmed that the VM disk was using ZFS.

## Replication Configuration

A Replication job was created for VM 100.

Configuration:

- VM ID: 100
- Target: proxmox-node2
- Enabled: Yes
- Schedule: */15
- State: OK

The replication job was:

    JobID: 100-0
    Target: local/proxmox-node2
    Last Sync: 2026-09-28 13:15
    Next Sync: 2026-09-28 13:30
    FailCount: 0
    State: OK
<img width="1919" height="858" alt="image" src="https://github.com/user-attachments/assets/6f81b1ad-d28c-4a32-9de5-f963b32bd12b" />

## Manual Synchronization

After creating the replication job, Schedule now was used to start an immediate synchronization.

The synchronization completed successfully.

This allowed the replica on proxmox-node2 to receive the VM disk data without waiting for the scheduled interval.

## How Replication Works

The initial synchronization transfers the required VM disk data to the target ZFS storage.

After the initial synchronization, Proxmox can synchronize changes between the source and replica.

Example:

    Initial Sync
    VM Disk
       |
       | Replication
       v
    Node 2 Replica

    Later changes
       |
       | Next Sync
       v
    Node 2 Replica

The complete disk does not need to be transferred again for every synchronization. ZFS replication can transfer the changes since the previous synchronization.
<img width="1919" height="590" alt="image" src="https://github.com/user-attachments/assets/280d2739-f1ee-48bb-94a3-17646b3b292c" />

## Replication vs Clone

Clone is used to create a new VM.

Example:

    Template
       |
       v
    Clone
       |
       v
    New VM

Replication is different:

    Existing VM
       |
       v
    Replication
       |
       v
    Replica on another Node

Clone is mainly used for VM deployment.

Replication is mainly used to keep a VM replica on another node for recovery purposes.

## Replication vs Backup

Backup creates a backup copy that can be restored later.

Replication keeps a synchronized VM disk replica on another node.

Example:

    Backup:
    VM
      |
      v
    Backup File
      |
      v
    Restore when required

    Replication:
    VM
      |
      v
    ZFS Replica on another Node

Replication is useful for disaster recovery, while Backup is used for restoring previous backup points.

## Replication and HA

Replication and HA have different responsibilities.

### Replication

Replication keeps VM data synchronized on another node.

### HA

HA manages VM availability and can manage the VM service during node failures or other HA events.

The basic concept is:

    Cluster
       |
       +-- HA
       |    |
       |    +-- VM availability
       |
       +-- Replication
            |
            +-- VM disk replica

Replication does not automatically mean that the VM will always start on the target node.

HA and Replication can be used together to provide a more complete availability and recovery design.


## What I Learned

- Proxmox Replication uses ZFS.
- Replication keeps a VM disk replica on another Proxmox node.
- The initial synchronization transfers the required VM data.
- Later synchronizations can transfer changes since the previous sync.
- Replication is different from Clone.
- Replication is different from Backup.
- HA manages VM availability, while Replication keeps VM data synchronized.
- A replication job can be scheduled using a regular interval.
- The replication status can be checked from the Proxmox GUI or with pvesr status.
- A boot disk cannot simply be unplugged while it is being used as the VM boot disk.
- Destructive recovery testing should be performed on a dedicated test VM rather than the main lab VM.

## Result

Task 10 was completed successfully.

VM 100 was configured for ZFS Replication from:

    proxmox-lab

to:

    proxmox-node2

The replication job was enabled with a 15-minute schedule and the manual synchronization completed successfully.

The lab now has practical experience with Proxmox ZFS Replication and its relationship with HA.
