# Task 18 — Veeam Backup & Replication with Proxmox VE

## Objective

The goal of this lab is to integrate Veeam Backup & Replication with Proxmox VE and perform a complete backup and restore workflow.

In this task, I will:

- Add Proxmox VE to Veeam.
- Deploy a Veeam Proxmox Worker.
- Configure a Veeam Backup Job.
- Back up a Proxmox VM.
- Store the backup in a Veeam Backup Repository.
- Restore the VM to a new location in Proxmox.
- Verify that the restored VM can boot successfully.

---

## Lab Environment

### Proxmox

- Proxmox Node: `proxmox-lab`
- Proxmox IP: `10.10.20.12`
- VM: `100`
- VM Name: `ubuntu-test01`
- VM Size: approximately `47 GB`
- Proxmox Storage: `d01`

### Veeam

- Veeam Server: `veeam01`
- Veeam Version: `13.1.1.18`
- Veeam Repository: `VEEAM01-Repository`
- Proxmox Worker: `veeam-worker01`

---

## Scenario

The environment requires a backup solution for Proxmox virtual machines.

Veeam Backup & Replication is used as the external backup platform.

The workflow is:

    Proxmox VM
        |
        v
    Veeam Proxmox Worker
        |
        v
    VEEAM01-Repository
        |
        v
    Restore to Proxmox
        |
        v
    New VM

---

## Step 1 — Add Proxmox to Veeam

Open Veeam Backup & Replication.

Go to:

    Backup Infrastructure
    → Managed Servers

Add the Proxmox VE server.

Use:

- Host: `10.10.20.12`
- Username: `root`
- SSH Port: `22`

The Proxmox SSH configuration must allow the authentication method required by Veeam.

After adding the Proxmox server, verify that Veeam can communicate with it.

-<img width="1248" height="790" alt="image" src="https://github.com/user-attachments/assets/2ee86be9-becf-4a38-b770-7a9d37349afc" />
--

## Step 2 — Deploy the Proxmox Worker

Go to:

    Backup Infrastructure
    → Backup Proxies

Add a new Proxmox VE Worker.

Use:

- Worker Name: `veeam-worker01`
- Host: `10.10.20.12`
- Storage: `d01`
- Maximum Concurrent Tasks: `1`

The Worker acts as a data mover between the Proxmox environment and the Veeam Backup Repository.

It is not a normal production workload VM.

After deployment, test the Worker.

Expected result:

    Test Worker → Successful

---
<img width="1294" height="748" alt="image" src="https://github.com/user-attachments/assets/359ae94a-093c-4e94-b537-adef05ef5950" />

## Step 3 — Configure the Backup Job

Go to:

    Home
    → Backup
    → Backup Job

Create a new Proxmox VE Backup Job.

Use the name:

    Proxmox-VM100-Backup

Select the Proxmox VM:

    VM 100 — ubuntu-test01

Make sure the VM is added only once.

---

## Step 4 — Configure Backup Storage

For the Backup Repository select:

    VEEAM01-Repository

The Veeam Repository is the location where the backup files are stored.

Important distinction:

- `d01` = Proxmox storage used by the VM.
- `VEEAM01-Repository` = Veeam backup storage.

The backup workflow is:

    VM 100
        ↓
    Proxmox
        ↓
    Veeam Worker
        ↓
    VEEAM01-Repository

For the lab, the default retention policy can be used.

Set the schedule to manual execution for the initial test.

---

## Step 5 — Start the Backup Job

Run:

    Proxmox-VM100-Backup

The job should process:

    ubuntu-test01

The successful lab result was:

- VM Size: `47 GB`
- Processed: `47 GB`
- Transferred: approximately `1.8 GB`
- Duration: approximately `4:49`
- Success: `1`
- Warnings: `0`
- Errors: `0`

The job used the Proxmox Worker:

    veeam-worker01

The backup was processed using HotAdd mode.

Expected result:

    Job finished successfully
<img width="1108" height="703" alt="image" src="https://github.com/user-attachments/assets/71139f84-2f72-4d07-8d0e-eb8ec47dceb6" />

---

## Step 6 — Understand the BIOS ID Warning

During the first backup attempt, Veeam reported:

    Another VM in the cluster or node has the same BIOS ID

The environment contained two VMs with the same name:

    VM 100 → ubuntu-test01
    VM 107 → ubuntu-test01

The important issue was not the VM name.

The restored VM had the same BIOS ID as the original VM.

Veeam uses VM identity information such as the BIOS ID to distinguish virtual machines.

After correcting the backup scope, the backup of VM 100 completed successfully.

---

## Step 7 — Open the Backup

After the backup finishes, go to:

    Home
    → Backups
    → Disk

Expand:

    Proxmox-VM100-Backup

The restore point should contain:

    ubuntu-test01

Important restore options include:

- Instant Recovery
- Entire VM
- Export Disks
- Publish Disks
- Restore Guest Files
- Application Items
- Export Backup
- Scan Backup
- Properties

For this lab, use:

    Entire VM

because the goal is to restore the complete virtual machine.

---

## Step 8 — Start Entire VM Restore

Select:

    ubuntu-test01

Choose:

    Entire VM

The Restore Mode screen provides two main choices:

    Restore to the original location

or:

    Restore to a new location, or with different settings

For this lab, select:

    Restore to a new location, or with different settings

This prevents the restore operation from replacing the original VM.

---

## Step 9 — Select Restore Storage

The VM contained two virtual disks:

    Disk 1 → 10 GB
    Disk 2 → 37 GB

Select:

    d01

for both disks.

The Veeam Backup Repository is the source of the backup.

The Proxmox `d01` storage is the destination for the restored VM disks.

The restore workflow is:

    VEEAM01-Repository
        ↓
    Veeam Worker
        ↓
    Proxmox
        ↓
    d01
        ↓
    Restored VM disks

---

## Step 10 — Configure the New VM Name

Use a different name for the restored VM.

Example:

    ubuntu-test01re

Do not use the original VM name if the original VM is still present.

The restored VM must also use a new VM ID.

For example:

    VM ID 111

The original VM remains:

    VM 100 — ubuntu-test01

---

## Step 11 — Configure Network Mapping

The restore summary showed:

    Network adapter mapping:
    vmbr1 → vmbr1

This keeps the restored VM on the isolated lab network.

No changes are required to:

- `vmbr0`
- `ens4`

The restored VM can be tested without changing the main Proxmox network.

---

## Step 12 — Start the Restore

Review the restore summary.

Expected configuration:

    Original name:
    ubuntu-test01

    New name:
    ubuntu-test01re

    Target:
    proxmox-lab

    Storage:
    d01

    Network:
    vmbr1

Start the restore.

The Veeam Worker performs the data movement.

---

## Step 13 — Verify the Restore Job

The restore session should show:

    Using worker veeam-worker01

    Restore task started

    Restore in progress

    Trying to restore disks in the "HotAdd" mode

    All disks were restored in the "HotAdd" mode

    Finalizing restore

    Restore finished

Expected result:

    Status: Success

---

## Step 14 — Verify the Restored VM in Proxmox

Open the Proxmox GUI.

The new VM should appear as:

    ubuntu-test01re

The original VM remains:

    100 — ubuntu-test01

The restored VM uses the new storage location:

    d01

--
<img width="1264" height="751" alt="image" src="https://github.com/user-attachments/assets/5bef720c-4ea5-4fc9-b96b-8d9f7095cde7" />

## Step 15 — Boot Test

Start:

    ubuntu-test01re

Open the Proxmox Console.

Verify that Ubuntu starts successfully.

The lab restore was successful and the restored VM completed the boot process.
<img width="1917" height="662" alt="image" src="https://github.com/user-attachments/assets/cd470baa-3823-41e9-8591-5601e901efb4" />

---

## Verification

The complete backup and restore workflow was verified:

    Proxmox VM 100
        ↓
    Veeam Backup Job
        ↓
    VeeAM01-Repository
        ↓
    Veeam Worker
        ↓
    Restore to new Proxmox VM
        ↓
    ubuntu-test01re
        ↓
    Successful Boot

The final restore test confirmed that the backup could be used to recover a working Proxmox virtual machine.

---

## Important Concepts

### Proxmox Storage vs Veeam Repository

Proxmox storage such as `d01` stores the live VM disks.

The Veeam Backup Repository stores backup data.

They have different purposes.

### Veeam Worker

The Proxmox Worker is responsible for data movement during Veeam operations.

It allows Veeam to efficiently process Proxmox workloads.

### Backup vs Restore

Backup protects the VM by creating a recoverable copy.

Restore uses that copy to recreate the VM.

A backup should be considered useful only after restore procedures have been tested.

### HotAdd

HotAdd is a data transfer method used by the Veeam Worker.

In this lab, both the backup and restore operations successfully used HotAdd mode.

---

## Troubleshooting Notes

### Problem — Worker deployment failed

The Worker deployment initially failed because the `ceph-vm` storage became inactive.

The Worker was later deployed successfully using `d01`.

### Problem — Worker network

The Worker was initially deployed on the internal Proxmox network.

The Worker eventually became operational and passed the Veeam Worker test successfully.

### Problem — Duplicate BIOS ID

Two VMs existed with the same VM name and the restored VM also had the same BIOS ID as the original.

Veeam initially excluded the VM because another VM had the same BIOS ID.

After correcting the backup scope, VM 100 was backed up successfully.

### Problem — VM Name

The restored VM was given a new name:

    ubuntu-test01re

This prevented confusion with the original:

    ubuntu-test01



## What I Learned

- How Veeam integrates with Proxmox VE.
- How to deploy a Proxmox Veeam Worker.
- How the Worker performs data movement.
- How to create a Proxmox Backup Job.
- How Veeam Backup Repositories differ from Proxmox Storage.
- How to perform an Entire VM Restore.
- How to restore a VM to a different location.
- How to select Proxmox storage during restore.
- How to map VM networks during restore.
- Why VM identity such as BIOS ID matters.
- How to verify a successful restore by booting the restored VM.
- Why backup testing should include an actual restore.

---

## Final Result

Task 18 was completed successfully.

The lab demonstrated a complete enterprise-style Veeam workflow:

    Backup
    → Repository
    → Worker
    → Restore
    → New Proxmox VM
    → Successful Boot

The original VM remained available, while the restored VM was created separately for recovery testing.
