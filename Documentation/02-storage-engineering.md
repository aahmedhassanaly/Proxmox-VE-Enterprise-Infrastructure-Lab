# Task 2 — Storage Engineering

## Objective

The goal of this task was to prepare the additional disk on the Proxmox server and use it as a Proxmox Directory Storage.

The task also included adding a virtual disk from the new storage to an Ubuntu test VM.

---

## 1. Storage Architecture

The Proxmox server has two main disks:

- `/dev/sda` — Proxmox operating system disk
- `/dev/sdb` — additional disk for the storage lab

The storage architecture used in this task is:

    /dev/sdb
        ↓
    Filesystem
        ↓
    Directory Storage
        ↓
    storage01
        ↓
    VM Disk
        ↓
    Ubuntu VM

This shows the difference between a physical disk and a Proxmox Storage.

---

## 2. Prepare the Additional Disk

The additional disk was identified from:

    Node → Disks

The disk used for this task was:

    /dev/sdb

The disk size was approximately 537 GB.

The disk was wiped before creating the new storage.

The Proxmox system disk `/dev/sda` was not modified.

### Why Wipe the Disk?

Wiping removes existing partition and filesystem information so the disk can be prepared from a clean state.

It does not mean that a new filesystem has already been created.

---

## 3. Create Proxmox Directory Storage

A new Directory Storage was created from the Proxmox GUI.

Path:

    Node → Disks → Directory → Create Directory

The additional disk was used to create the new storage.

Configuration:

| Setting | Value |
|---|---|
| Disk | `/dev/sdb` |
| Filesystem | `ext4` |
| Storage Name | `storage01` |
| Storage Type | Directory |

The new storage was then added to:

    Datacenter → Storage

---

## 4. Storage Content

The new storage was configured for the required content types.

Enabled:

- Disk image
- ISO image

Unnecessary content types were not enabled.

This is important because different Proxmox storage types can be used for different purposes.

For this lab, the main purpose of `storage01` is VM storage.

---

## 5. Verify the New Storage

The new storage was checked from:

    Datacenter → Storage

The storage appeared as:

    storage01

The storage is based on the additional `/dev/sdb` disk.

The existing `local` storage remains separate and continues to use the Proxmox system storage.

The basic architecture is:

    Proxmox
    |
    +-- local
    |     └── Proxmox system storage
    |
    +-- storage01
          └── Additional disk

---

## 6. Add a VM Disk

The existing Ubuntu test VM was used:

    VM ID: 100
    Name: ubuntu-test01

A new virtual disk was added from:

    VM → Hardware → Add → Hard Disk

Configuration:

| Setting | Value |
|---|---|
| Storage | `storage01` |
| Size | 10 GB |
| Bus | SCSI |
| Cache | No cache |
| Discard | On |
| IO Thread | On |

The existing operating system disk was not modified.
<img width="1524" height="601" alt="image" src="https://github.com/user-attachments/assets/b8b2f4e6-cc06-41f0-8116-b0902747a61f" />

---

## 7. Verify the Virtual Disk

After adding the disk, the Ubuntu VM detected a new 10 GB disk.

Inside Ubuntu, `lsblk` showed:

    sdb    10G    disk

The disk did not have a filesystem because it was added as a new virtual disk.

This demonstrates the difference between:

- Proxmox Storage
- Virtual Disk
- Guest Operating System Filesystem

The final flow is:

    /dev/sdb on Proxmox
          ↓
    storage01
          ↓
    10 GB Virtual Disk
          ↓
    Ubuntu VM
          ↓
    /dev/sdb

---

## 8. Storage Troubleshooting

A simple troubleshooting scenario was reviewed for an unavailable Proxmox Storage.

The troubleshooting approach is:

    Problem
       ↓
    Check the physical disk
       ↓
    Check the filesystem
       ↓
    Check the mount
       ↓
    Check Proxmox Storage
       ↓
    Verify the VM disk

Useful checks include:

    pvesm status

and, when required:

    findmnt

The important point is to identify where the storage chain is broken instead of running random commands.

---

## 9. Skills Practiced

- Proxmox Storage concepts
- Disk identification
- Disk preparation
- Directory Storage
- ext4 filesystem
- Proxmox Storage configuration
- VM disk management
- SCSI virtual disks
- Storage verification
- Basic storage troubleshooting
- Understanding the relationship between physical disks, Proxmox storage, and VM disks

---

## 10. Assessment

| Skill | Level |
|---|---:|
| Proxmox Storage concepts | 3 — Can implement |
| Disk identification | 3 — Can implement |
| Directory Storage | 3 — Can implement |
| VM Disk management | 3 — Can implement |
| Storage troubleshooting | 2 — Understand concepts |
| Storage architecture | 3 — Can implement |

### Overall Level

**3 — Can Implement**

The storage configuration was successfully implemented and tested with a real Ubuntu VM.

More advanced storage troubleshooting and storage technologies will be covered in later tasks.

---

## 11. Task Result


**Task 2 — Completed**

Completed:

- Identified `/dev/sdb`
- Prepared the additional disk
- Created a Directory Storage
- Created `storage01`
- Configured storage content
- Added a 10 GB virtual disk to `ubuntu-test01`
- Verified the disk inside Ubuntu
- Reviewed the basic storage troubleshooting process

