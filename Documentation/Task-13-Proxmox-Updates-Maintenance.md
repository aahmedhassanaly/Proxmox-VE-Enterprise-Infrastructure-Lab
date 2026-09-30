# Task 13 - Proxmox Updates & Maintenance

## Objective

The goal of this task is to learn how to safely update and maintain a Proxmox node in a cluster environment.

The main focus is:

- Repository management
- Proxmox package updates
- No-Subscription repository
- Maintenance Mode
- Safe node maintenance
- Kernel updates
- Reboot planning
- Post-maintenance verification
- Returning the node to normal HA operation

---

## Lab Environment

- Proxmox Cluster: `proxmox-lab`
- Node under maintenance: `proxmox-lab`
- Second Node: `proxmox-node2`
- Proxmox VE: 9.x
- HA: Enabled
- Cluster: 2 Nodes
- Quorum: Enabled

---

## Scenario

The Proxmox administrator needs to update `proxmox-lab`.

The node is part of a production-style cluster and has HA resources.

The administrator must:

1. Check the repositories.
2. Check available updates.
3. Prepare the node for maintenance.
4. Move HA resources away from the node.
5. Update Proxmox packages.
6. Reboot the node because a new kernel was installed.
7. Verify the node after reboot.
8. Return the node to normal HA operation.

---

## Repository Check

Open:

`proxmox-lab → Updates → Repositories`

The node initially had an enabled Proxmox Enterprise Repository.

The repository returned:

`401 Unauthorized`

This happened because the server did not have a valid Proxmox subscription.

The lab uses the No-Subscription repository instead.

The Enterprise repository was disabled.

The No-Subscription repository remained enabled:

`http://download.proxmox.com/debian/pve trixie pve-no-subscription`

---

## Refresh Package Information

From:

`proxmox-lab → Updates`

click:

`Refresh`

The package lists were successfully updated.

The final result was:

`TASK OK`

This confirmed that APT could successfully access the required repositories.
<img width="1353" height="668" alt="image" src="https://github.com/user-attachments/assets/17c4122b-3a3e-4029-b4f0-6739c08163fe" />

---

## Pre-Maintenance Check

Before updating the node, check:

`Datacenter → HA → Status`

Verify:

- Cluster quorum is OK.
- Both nodes are online.
- HA is active.
- The target node can be placed into maintenance.
- HA resources can run on the second node.

Do not start the upgrade before checking the cluster state.

---

## Maintenance Mode

In this Proxmox VE version, the GUI did not provide the Maintenance Mode action directly.

The node was placed into Maintenance Mode using:

`ha-manager crm-command node-maintenance enable proxmox-lab`

Maintenance Mode tells the HA system that the node should be taken out of service.

HA resources can then be moved to another available node.

After enabling Maintenance Mode, verify the state from:

`Datacenter → HA → Status`

The node should show:

`maintenance mode`

---

## Package Upgrade

After the node was prepared for maintenance, the Proxmox packages were updated.

The update completed successfully.

Important packages that were updated included:

- `pve-manager` → 9.2.21
- `qemu-server` → 9.2.10
- Kernel package → 6.12.111-1

The update process also completed the required initramfs and GRUB processing.

The final message was:

`Your System is up-to-date`
<img width="1912" height="894" alt="image" src="https://github.com/user-attachments/assets/6eb67edc-6f04-4106-9e98-b1642454a927" />

---

## Kernel Update

A new Linux kernel was installed during the update.

The installed kernel package was:

`6.12.111-1`

A kernel update normally requires a reboot before the running system uses the new kernel.

Therefore, the node was rebooted after the package upgrade.

---

## Reboot

Before rebooting, confirm that:

- The node is in Maintenance Mode.
- HA resources are running on another node.
- Cluster quorum is OK.

Then reboot the node from the Proxmox GUI:

`proxmox-lab → System → Reboot`

Wait for the node to return online.

---

## Post-Reboot Verification

After the reboot, verify:

- Node is online.
- Proxmox GUI is accessible.
- Proxmox version is updated.
- Kernel version is updated.
- Cluster is quorate.
- HA services are healthy.
- No unexpected errors are present.

The Proxmox version was checked after the reboot and showed the updated version.

---

## Exit Maintenance Mode

After successful verification, remove the node from Maintenance Mode:

`ha-manager crm-command node-maintenance disable proxmox-lab`

Then open:

`Datacenter → HA → Status`

Verify:

- `proxmox-lab` → active
- `proxmox-node2` → active
- Quorum → OK
- HA resources return to the normal HA state

---

## Safe Maintenance Workflow

The complete workflow used in this task was:

`Repository Check`
  
→ `Refresh Updates`
  
→ `Cluster / HA Check`
  
→ `Maintenance Mode`
  
→ `Package Upgrade`
  
→ `Kernel Update`
  
→ `Reboot`
  
→ `Post-Reboot Verification`
  
→ `Exit Maintenance Mode`

This is safer than updating a cluster node without planning the maintenance process.

---

## Important Notes

### Enterprise Repository

The Enterprise repository requires a valid Proxmox subscription.

For this lab environment, the No-Subscription repository is used instead.

### Maintenance Mode

Maintenance Mode is used when a Proxmox node needs planned maintenance.

It helps the administrator take the node out of normal HA service before performing maintenance.

### Reboot

Not every package update requires an immediate reboot.

However, when a new kernel is installed, a reboot is normally required to start using the new kernel.

### Verification

Never assume that an update succeeded only because the package installation finished.

Always verify:

- Proxmox version
- Kernel
- Node status
- Cluster status
- HA status

---

## What I Learned

- How to manage Proxmox repositories.
- How to identify an Enterprise Repository authentication problem.
- How to use the No-Subscription repository in a lab.
- How to refresh package information.
- How to prepare a Proxmox node for maintenance.
- How Maintenance Mode works with HA.
- How to perform a Proxmox package upgrade.
- Why a kernel update may require a reboot.
- How to verify a node after maintenance.
- How to return a node to normal HA operation.

---

## Result

Task 13 was completed successfully.

The `proxmox-lab` node was safely updated and rebooted.

The node returned online successfully, the new Proxmox version was verified, and the node was removed from Maintenance Mode.

The cluster returned to normal HA operation.
