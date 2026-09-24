# Task 06 — Proxmox Backup & Restore

## Objective

The goal of this task is to learn how to create, verify, and restore VM backups in Proxmox.

In a real enterprise environment, backups are important for recovering a VM after:

- System failure
- Data corruption
- Configuration problems
- Accidental changes
- VM deletion
- Disaster recovery

This task focuses on Proxmox Backup and Restore using the Proxmox GUI.

---

## Lab Environment

### Proxmox Node

- Node: `proxmox-lab`

### Test VM

- VM ID: `104`
- VM Name: `win`

### Backup Storage

- Storage: `d01`
- Path: `/mnt/pve/d01`

### Backup Format

- `vma.zst`
- Compression: `ZSTD`
- Backup Mode: `Snapshot`

---

##  Check Backup Storage

Open the Proxmox Web GUI.

Go to:

**Datacenter → Storage**

Check the available storage.

The lab contains:

- `local` → `/var/lib/vz`
- `d01` → `/mnt/pve/d01`

Both storages support backup content.

For this task, use:

**`d01`**

This keeps the backup separate from the VM's main storage.

---

## Select the Test VM

Open the VM:

**VM 104 — win**

Make sure the VM is working correctly before creating the backup.

Check:

- VM status
- Disk
- Memory
- CPU
- Network
- Guest OS

The VM should be in a known good state before creating the backup.

---

##  Create a VM Backup

From the Proxmox GUI:

**VM 104 → Backup → Backup now**

Configure:

- Storage: `d01`
- Mode: `Snapshot`
- Compression: `ZSTD`
- Notification mode: Auto

Use a useful backup note, for example:

**Before Recovery Test**

Then start the backup.

---

## Monitor the Backup

Open the Proxmox task window and monitor the backup.

A successful backup should show information similar to:

    INFO: starting new backup job
    INFO: Starting Backup of VM 104
    INFO: backup mode: snapshot
    INFO: creating vzdump archive

The backup should finish with a successful status.

---
<img width="1919" height="864" alt="image" src="https://github.com/user-attachments/assets/17fb66b9-c4ba-4737-9b09-8b4450473f78" />

## Task 5 — Understand the Backup File

After the backup finishes, Proxmox creates a backup archive on the selected storage.

Example:

    vzdump-qemu-104-2026_09_24-11_21_10.vma.zst

The important parts are:

- `vzdump` → Proxmox backup system
- `qemu` → QEMU/KVM virtual machine
- `104` → VM ID
- Date and time → Backup creation time
- `.vma.zst` → Compressed Proxmox VM backup archive

The backup is stored on:

    /mnt/pve/d01/dump/

---


---

##  Verify the Backup

Go to:

**Datacenter → Storage → d01 → Backups**

Find the backup of VM 104.

Verify:

- VM ID
- Backup date
- Backup size
- Backup format
- Backup status

The backup must be visible before starting the restore test.
<img width="1917" height="879" alt="image" src="https://github.com/user-attachments/assets/acacd526-cb6e-489c-aaf9-6f5e64a641a5" />

---

## Snapshot vs Backup

A snapshot and a backup are not the same thing.

### Snapshot

A snapshot creates a recovery point for a VM.

Typical use:

- Before configuration changes
- Before software installation
- Before testing

A snapshot is mainly useful for quick rollback.

### Backup

A backup creates a separate backup archive.

Typical use:

- Disaster recovery
- VM recovery
- Long-term protection
- Recovering a deleted or damaged VM

A backup can be stored on separate storage.

### Simple Difference

**Snapshot = Quick rollback**

**Backup = Recovery copy**

---

## Start a Restore Test

The purpose of this test is to verify that the backup can actually restore the VM.

From the backup storage:

**Datacenter → Storage → d01 → Backups**

Select the backup of VM 104.

Click:

**Restore**

---

## Restore as a New VM

Do not overwrite the original VM.

Use a new VM ID.

Example:

- Original VM: `104`
- Restored VM: `105`

This protects the original VM and allows us to test the backup safely.

Select the destination storage according to the available lab storage.

Start the restore operation.

---

## TMonitor the Restore

Monitor the Proxmox task window.

The restore process should:

1. Read the backup archive.
2. Create the VM configuration.
3. Restore the VM disks.
4. Restore the VM hardware configuration.
5. Finish successfully.

Wait until the restore task is complete.

---



## — Verify the Guest OS

Open the VM console.

Confirm that Windows starts correctly.

Verify that:

- Windows boots normally.
- Applications are available.
- VM configuration is present.
- The restored system has the expected settings.

The restored VM should represent the state captured by the backup.

---<img width="1918" height="963" alt="image" src="https://github.com/user-attachments/assets/f3b496ef-f980-443d-8a6f-16302e0e128e" />


##  Avoid Network Identity Problems

When restoring a VM as a test copy, be careful with its network adapter.

If the original and restored VMs are connected to the same network at the same time, they may have the same system identity or network configuration.

For a safe restore test, the restored VM can initially be kept disconnected from the network.

After verifying the operating system, network configuration can be changed before using the VM in the production network.

---

## Backup and Restore Workflow

The complete workflow used in this task is:

    VM 104
       |
       v
    Create Backup
       |
       v
    Storage: d01
       |
       v
    VMA.ZST Backup
       |
       v
    Verify Backup
       |
       v
    Restore
       |
       v
    New VM 105
       |
       v
    Start and Verify



## Important Notes

### Snapshot is not a Backup

A snapshot should not be considered a complete backup strategy.

Snapshots are useful for short-term rollback.

Backups should be stored separately and used for recovery.

### Backup Storage

Backup storage should have enough free space for the required VM backups.

In a production environment, backup storage should ideally be separated from the main VM storage.

### Backup Verification

Creating a backup is not enough.

A backup should also be tested by restoring it.

A backup that has never been restored has not been fully verified.

---


## What I Learned

After completing this task, I understand:

- How Proxmox creates VM backups.
- How to select backup storage.
- How Snapshot backup mode works.
- What ZSTD compression does.
- The role of QEMU Guest Agent during backup.
- How to verify a backup.
- How to restore a VM from a backup.
- How to restore a VM as a new VM.
- The difference between snapshots and backups.
- Why backup restore testing is important.

---

## Task Result

**Task 06 — Proxmox Backup & Restore: Completed**

VM 104 was successfully backed up to `d01`.

The backup was verified and restored as VM 105.

The restored Windows VM booted successfully.

This task completed the basic Proxmox VM backup and recovery workflow.
