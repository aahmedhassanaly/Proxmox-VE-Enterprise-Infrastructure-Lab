# Task 8 - Proxmox High Availability (HA)

## Objective

The goal of this lab was to configure Proxmox High Availability and test automatic VM relocation between two Proxmox nodes.

The lab also demonstrated an important HA requirement: VM storage must be available on the target node for a successful relocation.

## Lab Environment

- Proxmox Cluster: `proxmox-lab`
- Node 1: `proxmox-lab`
- Node 2: `proxmox-node2`
- Test VM: `ubuntu-test01`
- VM ID: `100`
- HA Resource: `vm:100`

## HA Resource

VM 100 was added as an HA Resource.

The Request State was configured as:

    started

This tells Proxmox HA that the VM should remain running and that HA should manage its availability.

Important Request State options:

- `started` - HA tries to keep the VM running.
- `stopped` - HA keeps the VM stopped.
- `ignored` - HA temporarily ignores the resource.
- `disabled` - HA management of the resource is disabled.

The lab used `started` because the goal was to keep VM 100 available.
<img width="1919" height="665" alt="image" src="https://github.com/user-attachments/assets/48bf6377-94c6-4c0c-ab5a-92a8fd4e3613" />

## HA Resource Settings

The HA Resource included settings such as:

- Request State: `started`
- Max Restart: `1`
- Max Relocate: `1`
- Fallback: enabled
- Auto-Rebalance: enabled

Max Restart controls how many restart attempts HA can make when a VM fails.

Max Relocate controls relocation attempts when HA needs to move the VM to another node.

## Node Affinity Rule

A Node Affinity Rule was created for VM 100.

Configuration:

- Resource: `100`
- Affinity: `Prefer Nodes`
- Nodes:
  - `proxmox-lab`
  - `proxmox-node2`
- Strict: disabled

The purpose of the rule is to define preferred nodes for the VM.

`Prefer Nodes` means the selected nodes are preferred, but the VM is not strictly locked to them.

The difference is:

- HA Resource defines how HA should manage the VM.
- Node Affinity defines which nodes HA should prefer for the VM.

## Maintenance Mode Test

The node `proxmox-lab` was placed into HA Maintenance Mode.

The command used was:

    ha-manager crm-command node-maintenance enable proxmox-lab

HA detected that the node was in Maintenance Mode and attempted to relocate VM 100.

## Initial HA Migration Problem

The first automatic relocation failed.

The VM disk was using storage:

    d01

The `d01` storage was available only on `proxmox-lab` and was not available on `proxmox-node2`.

The migration failed with:

    storage 'd01' is not available on node 'proxmox-node2'

This demonstrated that HA does not automatically make local storage available on another node.
<img width="1919" height="729" alt="image" src="https://github.com/user-attachments/assets/a3084719-a32d-4bb4-ae46-976e64f21a15" />

## Storage Fix

The active VM disk was moved from `d01` to `local`.

The disk migration completed successfully.

The VM configuration then showed:

    scsi0: local:100/vm-100-disk-0.qcow2

The old disk was temporarily kept as an unused disk until the HA test was completed.

After the storage dependency was removed, the VM became suitable for relocation to the second node.

## Automatic HA Relocation

The node `proxmox-lab` was placed into Maintenance Mode again.

HA automatically relocated VM 100 to `proxmox-node2`.

Final HA status showed:

    quorum OK
    fencing armed
    lrm proxmox-lab (maintenance mode)
    lrm proxmox-node2 (active)
    service vm:100 (proxmox-node2, started)

This confirmed that HA successfully moved the VM without performing a manual migration.
<img width="1902" height="887" alt="image" src="https://github.com/user-attachments/assets/c7e8d99f-4bc7-4e16-9e96-a4cc23ffd8b9" />


## Verification

The final HA state confirmed:

- Cluster quorum was OK.
- Fencing was armed.
- `proxmox-node2` was active.
- `proxmox-lab` was in Maintenance Mode.
- VM 100 was running on `proxmox-node2`.
- Automatic HA relocation was successful.

## What I Learned

- HA manages VM availability across Proxmox nodes.
- `started` means HA should keep the VM running.
- `stopped` means HA should keep the VM stopped.
- `ignored` makes HA ignore the resource.
- `disabled` disables HA management for the resource.
- HA Resources define how a VM should be managed.
- Node Affinity Rules define preferred nodes for a VM.
- Maintenance Mode can trigger HA relocation.
- Local storage can prevent HA relocation when it is not available on the target node.
- Storage availability is an important part of a successful HA design.
- Fencing is an important component of HA.
- HA can perform automatic VM relocation without manually starting a migration.

## Result

Task 8 was completed successfully.

VM 100 was configured as an HA Resource and was automatically relocated from `proxmox-lab` to `proxmox-node2` when `proxmox-lab` entered Maintenance Mode.
