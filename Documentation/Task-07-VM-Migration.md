# Task 07 - VM Migration

## Objective

The goal of this lab is to practice Proxmox VM migration between two Proxmox nodes.

In this lab, I practiced:

- Offline Migration
- Live Migration
- Local Storage Migration
- Migration requirements
- VM verification after migration
- Troubleshooting a migration failure
- Proxmox VE version compatibility

The lab uses a two-node Proxmox cluster.

---

## Lab Environment

| Component | Node 1 | Node 2 |
|---|---|---|
| Hostname | proxmox-lab | proxmox-node2 |
| Proxmox VE | 9.2 | 9.2 |
| Private IP | 10.10.20.12 | 10.10.20.14 |
| CPU | 8 vCPU | 4 vCPU |
| RAM | 64 GB | 16 GB |
| Cluster | proxmox-lab | proxmox-lab |

Test VM:

- VM ID: 100
- VM Name: ubuntu-test01
- OS: Ubuntu Server
- RAM: 2048 MB
- CPU: 1 Core
- Disk: 32 GB
- Storage: local
- Network Bridge: vmbr1

---

## Scenario

The virtualization team needs to move a virtual machine from one Proxmox node to another node.

First, the VM will be moved while it is powered off.

After that, the same VM will be moved while it is running using Live Migration.

This simulates a common enterprise scenario such as:

- Host maintenance
- Hardware maintenance
- Resource balancing
- Moving workloads between hosts
- Reducing service downtime

---

## Step 1 - Verify the Cluster

Check the cluster status:

    pvecm status

The cluster should show two nodes and a working quorum.

Example:

    Cluster information
    --------------------
    Name:           proxmox-lab
    Nodes:          2
    Expected votes: 2
    Total votes:    2
    Quorate:        Yes

Also verify the nodes:

    pvecm nodes

---

## Step 2 - Verify the VM

On the source node, check the VM:

    qm list

Check the VM configuration:

    qm config 100

The VM should contain its local disk and configuration.

Example:

    name: ubuntu-test01
    memory: 2048
    cores: 1
    scsi0: local:100/vm-100-disk-0.qcow2,size=32G

---

## Step 3 - Check Storage

Check Proxmox storage:

    pvesm status

The migration in this lab uses local storage.

The target node must have a storage with the required content type available.

In this lab:

    Source Storage: local
    Target Storage: local

---

## Step 4 - Offline Migration

The first migration was performed while the VM was stopped.

From the Proxmox GUI:

1. Select VM 100.
2. Make sure the VM is stopped.
3. Select **More**.
4. Select **Migrate**.
5. Select the target node:
   `proxmox-node2`
6. Set **Online** to Off.
7. Select target storage:
   `local`
8. Start the migration.

The migration copied the VM disk from Node 1 to Node 2.

The migration completed successfully.

Example migration result:

    copying local disk images
    successfully imported 'local:100/vm-100-disk-0.qcow2'
    migration finished successfully
    TASK OK

---<img width="1917" height="963" alt="image" src="https://github.com/user-attachments/assets/3a1269ee-580e-40cc-90c2-f086ec11f033" />


## Step 5 - Verify the VM on Node 2

After the migration, check the VM from Node 2:

    qm list

Check its configuration:

    qm config 100

The VM should still have:

- The same VM ID
- The same VM name
- The same CPU configuration
- The same memory configuration
- The same virtual disk
- The same virtual NIC configuration

Start the VM:

    qm start 100

Then verify that Ubuntu boots correctly.

---

## Step 6 - Prepare for Live Migration

Live Migration requires the VM to be running.

Start the VM:

    qm start 100

Verify:

    qm status 100

Expected result:

    status: running

The target node must also be compatible with the VM configuration.

For this lab, both Proxmox nodes were upgraded to the same major Proxmox VE version.

---

## Step 7 - Perform Live Migration

From the Proxmox GUI:

1. Select VM 100.
2. Select **More**.
3. Select **Migrate**.
4. Select the target node.
5. Enable **Online**.
6. Select target storage:
   `local`
7. Start the migration.

The VM remained powered on during the migration.

The migration completed successfully.

The Ubuntu VM was moved to the other Proxmox node while it was still running.

---

## Step 8 - Verify Live Migration

After the migration, verify the VM on the target node:

    qm list

Check the VM status:

    qm status 100

Expected result:

    status: running

Check the VM configuration:

    qm config 100

The VM should still have the same configuration.

The important result is that the VM was running before, during, and after the migration.
<img width="1917" height="963" alt="image" src="https://github.com/user-attachments/assets/d326965f-2847-46cd-8194-ba21be877aab" />
<img width="1914" height="840" alt="image" src="https://github.com/user-attachments/assets/e2b5b59b-8003-4034-a40e-79dace9f4adf" />

---

## Troubleshooting

During the first Live Migration attempt, the migration failed.

The important error was:

    Installed QEMU version '9.2.0' is too old to run machine type 'pc-i440fx-11.0+pve0'

The problem was caused by a Proxmox/QEMU version mismatch between the two nodes.

Node 1 was running:

    Proxmox VE 8.4.21
    QEMU 9.2.0

Node 2 was running:

    Proxmox VE 9.2.0
    QEMU 11.0.3

Because the target VM used a machine type that required a newer QEMU version, Live Migration could not start the VM on the target node.

---

## Fix

Node 1 was upgraded from Proxmox VE 8 to Proxmox VE 9.

Before the upgrade, the Proxmox repository was changed from Bookworm to Trixie:

    deb http://download.proxmox.com/debian/pve trixie pve-no-subscription

The available Proxmox version was verified:

    apt-cache policy proxmox-ve

The result showed:

    Installed: 8.4.0
    Candidate: 9.2.0

The upgrade was then performed:

    apt dist-upgrade

During the upgrade, configuration files were kept when required.

After the upgrade, Node 1 was running Proxmox VE 9.2.

Both nodes were then compatible for Live Migration.

---

## Important Migration Notes

### Offline Migration

Offline Migration requires the VM to be stopped.

Advantages:

- Simple
- Reliable
- Suitable for maintenance
- Does not require the VM to stay online

The VM experiences downtime during the migration.

### Live Migration

Live Migration moves a running VM between Proxmox nodes.

Advantages:

- Very low downtime
- Useful for host maintenance
- Useful for workload balancing
- Suitable for production maintenance scenarios

Live Migration requires compatible nodes and VM configuration.

---

## Verification Checklist

- [x] Two Proxmox nodes are available.
- [x] Both nodes are members of the same cluster.
- [x] Cluster has quorum.
- [x] VM 100 was migrated offline.
- [x] VM 100 was verified on the target node.
- [x] VM 100 was started successfully.
- [x] Live Migration was performed.
- [x] VM remained running during Live Migration.
- [x] VM was successfully moved to the target node.
- [x] Proxmox/QEMU version mismatch was identified.
- [x] Node 1 was upgraded to Proxmox VE 9.
- [x] Live Migration worked successfully after the upgrade.

---

## What I Learned

In this lab, I learned how Proxmox handles VM migration between cluster nodes.

I practiced both Offline Migration and Live Migration.

I also learned that VM migration is not only about storage and network configuration. The source and target Proxmox nodes must also support the VM hardware and QEMU machine type.

A version mismatch between Proxmox/QEMU nodes can prevent Live Migration.

I also learned how to troubleshoot a real migration error and fix it by upgrading the older Proxmox node.

---

## Result

The VM `ubuntu-test01` was successfully migrated between the two Proxmox nodes.

Offline Migration was completed successfully.

Live Migration was also completed successfully while the Ubuntu VM was running.

The final cluster contains two compatible Proxmox VE 9.2 nodes and is ready for the next Proxmox administration task.
