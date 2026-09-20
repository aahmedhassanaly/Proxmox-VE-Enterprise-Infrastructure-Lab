# Task 2 – Storage Engineering

## Objective

Build and test different Proxmox storage backends and understand how virtual disks are created, attached, detached, and restored.

## Environment

- Proxmox VE 8.4.21
- Node: `proxmox-lab`
- VM: `100 - ubuntu-test01`
- Additional disk: `/dev/sdb` (~537 GB)

---

## 1. Directory Storage

The additional disk `/dev/sdb` was prepared and configured as an ext4 filesystem.

A Proxmox Directory Storage named `storage01` was created and used for VM storage.

A 10 GB SCSI virtual disk was then created on `storage01` and attached to `ubuntu-test01`.

The disk was detected successfully inside Ubuntu.

This confirmed the complete flow:

`Physical Disk → Filesystem → Directory Storage → Virtual Disk → Guest OS`

---
<img width="1524" height="601" alt="image" src="https://github.com/user-attachments/assets/b8b2f4e6-cc06-41f0-8116-b0902747a61f" />

## 2. LVM Storage

The additional disk was reused to test Proxmox LVM storage.

The disk was added as an LVM storage backend and a 20 GB virtual disk was created from it.

The disk was attached to `ubuntu-test01` using the SCSI bus and was successfully detected by Ubuntu.

This demonstrated the difference between file-based storage and block-based storage:

`Directory Storage → VM disk stored as a file`

`LVM Storage → VM disk provided as a Logical Volume`

---
<img width="1527" height="598" alt="image" src="https://github.com/user-attachments/assets/354b279c-a11b-4c6b-8148-08702a67cbea" />

## 3. ZFS Storage

The additional disk was then reused for ZFS testing.

A ZFS storage named `zfs01` was created with:

- RAID Level: Single Disk
- Compression: Enabled
- ashift: 12

A virtual disk was created from the ZFS storage and attached to the Ubuntu VM.

Inside Ubuntu, the disk was formatted with ext4 and mounted at:

`/data`

A test file was created on the mounted filesystem.
<img width="1906" height="650" alt="image" src="https://github.com/user-attachments/assets/434fb728-2fec-4ac2-9354-1b0bb4bae9ed" />

---

## 4. Disk Detach and Re-attach Test

The ZFS-backed disk was detached from the VM without deleting the virtual disk.

The disk was then attached again to the same VM.

The filesystem was mounted again and the previously created test file was still available.

This confirmed that:

**Detach removes the disk from the VM hardware configuration, but does not delete the underlying disk or its data.**

---

## 5. Snapshot and Rollback Test

A test file was created on the ZFS-backed disk with the content:

`BEFORE SNAPSHOT`

A VM snapshot was created.

The file was then modified to:

`DATA AFTER SNAPSHOT`

The VM was rolled back to the previous snapshot.

After the rollback, the file content returned to:

`BEFORE SNAPSHOT`

The snapshot successfully included the VM disks stored on both local storage and `zfs01`.

---
<img width="1919" height="851" alt="image" src="https://github.com/user-attachments/assets/cbde1841-8fb3-4866-98d4-d59ea64d2798" />

## 6. Storage Troubleshooting

During the storage work, the additional disk had to be reused between different storage backends.

Proxmox reported that the disk or partition was still mounted when attempting to change its storage configuration.

The previous storage configuration was removed and the disk was cleaned before reusing it with another backend.

A snapshot attempt while the VM was running also failed with a QEMU/vdagent migration-related error.

The snapshot was retried with the VM powered off and completed successfully.

---

## Key Results

The lab covered practical use of:

- Directory Storage
- LVM Storage
- ZFS Storage
- Virtual disk creation
- SCSI virtual disks
- Disk detach and re-attach
- Filesystem mounting inside a VM
- VM snapshots
- Snapshot rollback
- Storage troubleshooting


