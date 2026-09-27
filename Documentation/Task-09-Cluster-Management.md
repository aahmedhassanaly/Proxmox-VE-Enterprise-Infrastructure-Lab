# Task 9 - Proxmox Cluster Management

## Objective

Learn how to manage and inspect a Proxmox Cluster from the GUI.

The lab focuses on Cluster Information, Cluster Nodes, Join Information,
Cluster Log, Datacenter Options, and the difference between HA Resource
settings, HA Shutdown Policy, and Maintenance Mode.

## Lab Environment

Cluster Name: proxmox-lab

Nodes:
- proxmox-lab - 10.10.20.12
- proxmox-node2 - 10.10.20.14

Number of Nodes: 2

Votes:
- proxmox-lab: 1
- proxmox-node2: 1

Corosync Link 0:
- proxmox-lab: 10.10.20.12
- proxmox-node2: 10.10.20.14

## Cluster Information

Path:

Datacenter → Cluster

The Cluster page shows the main cluster information.

### Cluster Name

The cluster name is:

proxmox-lab

This is the name of the complete Proxmox cluster, not the name of a single node.

### Config Version

The lab showed:

Config Version: 2

The configuration version is used by Proxmox to track cluster configuration changes.

It is not the Proxmox VE software version.

### Number of Nodes

The cluster contains two nodes:

- proxmox-lab
- proxmox-node2

## Cluster Nodes

The Cluster Nodes table showed:

| Node | ID | Votes | Link 0 |
|---|---:|---:|---|
| proxmox-lab | 1 | 1 | 10.10.20.12 |
| proxmox-node2 | 2 | 1 | 10.10.20.14 |

### Node ID

The Node ID is an internal identifier for each node in the cluster.

It is different from:
- IP address
- VM ID
- Cluster name

### Votes

Each node has one vote.

Total votes:

2

Votes are used for cluster quorum.

A two-node cluster can work in the lab, but production HA environments normally use more quorum votes to improve reliability.

### Link 0

Link 0 is used for cluster communication.

In this lab:

proxmox-lab
10.10.20.12
        |
        | Corosync
        |
10.10.20.14
proxmox-node2

This is cluster communication and is different from the VM network.

## Join Information

Path:

Datacenter → Cluster → Join Information

Join Information is used when a new Proxmox node needs to join an existing cluster.

The information can contain the cluster connection details and fingerprint information needed to verify the cluster.

The lab did not perform another Join operation because both required nodes were already members of the cluster.

Important:

Join Information is not VM Migration.

Joining a node:
- adds a Proxmox node to the cluster.

Migration:
- moves a VM or container between existing cluster nodes.

## Cluster Log

Path:

Datacenter → Cluster → Cluster Log

The Cluster Log shows cluster-related events.

Important columns include:

- Time
- Node
- Service
- PID
- User name
- Severity
- Message

Example events observed in the lab included:

- Successful authentication for root@pam.
- HA starting a VM.
- HA finishing a VM start operation.
- Cluster log service starting.

The Cluster Log is useful for troubleshooting because it helps identify:

- What happened
- When it happened
- Which node was involved
- Which service reported the event
- The severity
- The event details
<img width="1919" height="863" alt="image" src="https://github.com/user-attachments/assets/a0e8a892-b235-4e84-b430-83b7215bd6ec" />

## Datacenter Options

Path:

Datacenter → Options

Important options observed in the lab included:

- Migration Settings
- Replication Settings
- HA Settings
- Cluster Resource Scheduling
- Bandwidth Limits
- Maximal Workers/bulk-action
- Next Free VMID Range

These settings are at Datacenter level and can affect cluster-wide behavior.

The lab did not change sensitive cluster settings.

## HA Resource Settings vs HA Settings

This was an important part of the lab.

There are two different levels of HA configuration.

### HA Resource Settings

Path:

Datacenter → HA → Resources → VM 100 → Edit

These settings control how HA manages a specific resource.

The lab used VM 100.

Settings included:

- Request State: started
- Max. Restart: 1
- Max. Relocate: 1
- Fallback: enabled
- Auto-Rebalance: enabled

### Request State

The available states in the Proxmox VE 9.2 GUI were:

- started
- stopped
- ignored
- disabled

#### started

HA should keep the VM running.

If the VM fails, HA can try to restart or relocate it according to the HA configuration.

#### stopped

HA should keep the VM stopped.

#### ignored

HA does not actively manage the resource.

#### disabled

The HA resource is disabled.

Important:

The current GUI does not show "migrate" as a Request State.

The HA system can show "migrate" as an internal service state while a resource is being migrated.

For example:

service vm:100 (proxmox-lab, migrate)

After a successful migration, the service can return to:

service vm:100 (proxmox-node2, started)

## HA Resource Options

### Max. Restart

Defines how many restart attempts HA can make when a resource fails to start on a node.

Lab value:

Max. Restart = 1

### Max. Relocate

Defines how many relocate attempts HA can make when a resource cannot be started successfully on the current node.

Lab value:

Max. Relocate = 1

### Fallback

Allows HA to use another eligible node when the preferred node is not available.

### Auto-Rebalance

Allows HA to rebalance resources according to the cluster scheduling and affinity rules.

## HA Node Affinity Rule

The lab created a Node Affinity Rule for VM 100.

Configuration:

- Resource: VM 100
- Affinity: Prefer Nodes
- Strict: disabled
- Nodes:
  - proxmox-lab
  - proxmox-node2

This means VM 100 prefers the configured nodes but is not hard-pinned to only one node.

In Proxmox VE 9, HA Groups are deprecated and replaced by HA Node Affinity Rules.

## HA Settings

Path:

Datacenter → Options → HA Settings

The HA Settings are different from the settings of one HA Resource.

For example:

HA Resource Settings:
- Request State
- Max Restart
- Max Relocate
- Fallback
- Auto-Rebalance

These control a specific HA resource.

HA Settings:
- Shutdown Policy

This controls the general HA behavior when a cluster node is shutting down or rebooting.

## Shutdown Policy

The available policies are:

- migrate
- failover
- freeze
- conditional

### migrate

When a node receives a shutdown request, HA marks the node unavailable and tries to migrate its HA services to another eligible node before shutdown.

This requires that the services can actually be migrated to another suitable node.

### failover

HA stops the services on the node and allows them to be recovered on another node if the original node does not return soon.

### freeze

HA stops and freezes the services so they are not recovered until the original node becomes available again.

### conditional

Conditional automatically changes its behavior depending on whether the node is being rebooted or powered off.

For a planned shutdown/poweroff, HA stops the managed services so other nodes can take over.

For a planned reboot, HA freezes the services so they can continue on the same node after the reboot.

The default value in the lab was:

Default (conditional)

## Important Difference

The three concepts must not be confused.

### HA Resource Settings

Control:

"How should HA manage this VM?"

Example:

VM 100 → Request State = started

### HA Shutdown Policy

Controls:

"What should HA do when the Node itself is shutting down or rebooting?"

Example:

Datacenter → Options → HA Settings → Shutdown Policy

### Maintenance Mode

Controls:

"I want to take this Node out of HA service for planned maintenance."

When Maintenance Mode is enabled, HA marks the node unavailable for HA operation and attempts to migrate its HA-managed services to other eligible nodes.

Example from the lab:

proxmox-lab
    ↓
Maintenance Mode
    ↓
VM 100
    ↓
Migration
    ↓
proxmox-node2
    ↓
VM 100 = started

Maintenance Mode is therefore a separate operation from the Shutdown Policy.

## Simple Comparison

| Feature | Main Purpose | Scope |
|---|---|---|
| HA Resource Settings | Manage a specific VM/CT | One resource |
| Request State | Desired state of the resource | One resource |
| HA Shutdown Policy | Control HA behavior during node shutdown/reboot | Datacenter/Cluster |
| Maintenance Mode | Evacuate HA resources before planned node maintenance | One node |

## Practical Scenario

A virtualization administrator wants to maintain a Proxmox node.

A safe process is:

1. Check cluster health.
2. Check quorum.
3. Check HA resources.
4. Check that another eligible node is available.
5. Put the node into Maintenance Mode.
6. Allow HA resources to migrate.
7. Verify that no HA resources remain on the maintenance node.
8. Perform maintenance.
9. Disable Maintenance Mode.
10. Verify the cluster and HA status.


## Result

Task 9 was completed successfully.

The Proxmox cluster was inspected and managed from the GUI without removing nodes or making unsafe changes to the cluster configuration.
