# Task 03 — VM, Template, Clone, and Snapshot Management

## Objective

In this task, I practiced basic VM management in Proxmox VE.

The main goals were:

- Create a new virtual machine.
- Configure VirtIO.
- Install Windows.
- Enable and install QEMU Guest Agent.
- Create a VM Snapshot.
- Convert a VM to a Template.
- Create a Full Clone.
- Test Snapshot Rollback.

---

## 1. Create the Test VM


### VM Configuration

- **VM Name:** `dc01`
- **BIOS:** OVMF (UEFI)
- **Machine:** Q35
- **CPU:** 1 Socket / 2 Cores
- **CPU Type:** Host
- **Memory:** 4096 MB
- **Disk:** 32 GB
- **Disk Bus:** SCSI
- **Disk Format:** QCOW2
- **Network Model:** VirtIO
- **Bridge:** `vmbr0`
- **TPM:** Not configured

The VM was used as a safe test machine for the Task 3 exercises.

---

## 2. Install Windows

Windows was installed on the new VM.

After installation, I entered Windows and verified that the VM was working correctly.

---

## 3. QEMU Guest Agent

QEMU Guest Agent was enabled from Proxmox:

**VM → Options → QEMU Guest Agent → Enabled**

The VirtIO ISO was used to install the Guest Agent inside Windows.

The installation file was:

`guest-agent\qemu-ga-x86_64.msi`

After installation, the service can be checked from PowerShell:

    Get-Service QEMU-GA

The QEMU Guest Agent allows Proxmox to communicate with the guest operating system and provides additional VM management features.

---

## 4. Create a Snapshot

A Snapshot was created before testing Rollback.

### Snapshot Name

`clean-install`

The Snapshot operation completed successfully with:

    TASK OK

The Snapshot included the VM disks and EFI disk.

Snapshots are useful when we want to save the current state of a VM before making changes.

---

## 5. Convert the VM to a Template

The VM was converted to a Proxmox Template.

Before converting, the existing Snapshot had to be removed because Proxmox does not allow converting a VM that contains Snapshots into a Template.

The Template can be used as a base for creating new VMs.

### Important

A Windows VM should normally be generalized with Sysprep before being used as a Template for multiple Windows machines.

For this lab, the Template and Clone process was practiced on the test VM.
<img width="1919" height="696" alt="image" src="https://github.com/user-attachments/assets/d759eb03-7f2c-4914-b3fc-528ff95f753b" />

---

## 6. Full Clone

A Full Clone was created from the Template.

### Clone Configuration

- **Clone Type:** Full Clone
- **VM Name:** `dc01-clone`
- **VM ID:** `103`

A Full Clone creates an independent copy of the VM storage.

The Full Clone does not depend on the original Template after creation.
<img width="1919" height="895" alt="image" src="https://github.com/user-attachments/assets/f2dd7c76-f3d6-466e-86d4-e556d8724ffb" />

---

## 7. Snapshot on the Clone

After starting the cloned VM, another Snapshot was created.

### Snapshot Name

`before-revert-test`

The Snapshot operation completed successfully:

    TASK OK

The Proxmox output showed that the VM disks and EFI disk were snapshotted and the VM state was saved.

---

## 8. Test Rollback

To test the Snapshot, a simple change was made inside Windows.

A folder named:

    TEST-BEFORE-REVERT

was created on the Windows desktop.

Then the VM Snapshot was selected:

`before-revert-test`

The **Rollback** operation was used.

After the Rollback, Windows returned to the state saved in the Snapshot.

The test folder disappeared and the previous desktop state was restored.

This confirmed that the Snapshot and Rollback process was working correctly.

---

## 9. Snapshot vs Rollback

### Snapshot

A Snapshot saves the current state of a VM at a specific point in time.

Example:

    VM
     |
     +--- Snapshot
          |
          +--- Saved VM state
<img width="1907" height="722" alt="image" src="https://github.com/user-attachments/assets/b35b0c53-dfd2-4734-8289-ca62a46bf224" />

### Rollback

Rollback returns the VM to the state saved in the selected Snapshot.

Example:

    Current VM
        |
        | Rollback
        v
    Snapshot state

Rollback does not mean deleting the Snapshot.


---

## 10. Full Clone vs Linked Clone

### Full Clone

A Full Clone is a complete and independent copy of the VM.

Advantages:

- Independent from the source VM.
- Can continue working if the Template is deleted.
- Good for standalone lab machines.
- Uses more storage.

### Linked Clone

A Linked Clone uses the original/base storage as a reference.

Advantages:

- Uses less storage.
- Faster to create.
- Useful when creating many test VMs.

For this lab, **Full Clone** was used because the goal was to create an independent VM.

---

## 11. What I Learned

In this task, I practiced:

- VM creation.
- VM hardware configuration.
- VirtIO.
- QEMU Guest Agent.
- Snapshots.
- Rollback.
- Templates.
- Full Clones.
- The difference between Full Clone and Linked Clone.

The practical Snapshot test showed that Rollback can return the VM to its previous saved state, including the Windows desktop state.

---

## Task 3 Status

- [x] Create VM
- [x] Configure VirtIO
- [x] Install Windows
- [x] Enable QEMU Guest Agent
- [x] Install QEMU Guest Agent
- [x] Create Snapshot
- [x] Convert VM to Template
- [x] Create Full Clone
- [x] Test Snapshot Rollback

**Task 3 completed successfully.**
