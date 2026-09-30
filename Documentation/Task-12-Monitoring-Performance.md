# Task 12 - Proxmox Monitoring & Performance

## Objective

The goal of this task is to learn how to monitor Proxmox nodes and virtual machines and identify possible performance problems.

The main focus is:

- CPU usage
- Memory usage
- Load Average
- Swap usage
- Storage I/O
- I/O Delay
- VM performance
- Task History
- Performance troubleshooting

The task uses normal Proxmox GUI monitoring without making unnecessary changes to the lab.

---

## Lab Environment

- Proxmox Cluster: `proxmox-lab`
- Main Node: `proxmox-lab`
- Proxmox Version: PVE 9.2.20
- CPU: 8 vCPUs
- RAM: 62.79 GiB
- Test VM: VM 100
- VM Name: `ubuntu-test01`
- VM CPU: 1 vCPU
- VM RAM: 2 GiB

---

## Monitoring the VM

Open:

`VM 100 → Summary`

Check the following values:

- CPU Usage
- Memory Usage
- Disk Read
- Disk Write
- IO Delay
- Network Traffic
- HA State
- Node

The VM should be checked first when users report that a virtual machine is slow.
<img width="732" height="401" alt="image" src="https://github.com/user-attachments/assets/9a2f4909-6180-40fe-9ee3-f8ad037e5fcc" />

---

## VM Baseline

The observed VM values were:

- Status: Running
- HA State: Started
- Node: `proxmox-lab`
- CPU Usage: 100.19% of 1 CPU
- Memory Usage: 39.24 MiB of 2 GiB
- IO Delay: 0%

The VM had one vCPU, so approximately 100% CPU usage means that the single virtual CPU was fully used at the time of the measurement.

This does not mean that the Proxmox node CPU was fully used.

---

## Monitoring the Proxmox Node

Open:

`Node → proxmox-lab → Summary`

Check:

- CPU Usage
- RAM Usage
- Swap Usage
- Load Average
- Disk Usage
- CPU count
- Kernel Version

The node values were:

- CPU Usage: 14%
- CPU: 8 CPUs
- RAM Usage: 18.23 GiB of 62.79 GiB
- RAM Usage Percentage: 29.04%
- Disk Usage: 22.34 GiB of 48.98 GiB
- Disk Usage Percentage: 45.62%
- Swap Usage: N/A

These values did not show a clear resource bottleneck on the Proxmox node.

---<img width="1556" height="600" alt="image" src="https://github.com/user-attachments/assets/ec088f64-f963-43f0-9b61-76d464735bc0" />


## Understanding Load Average

The node showed:

`2.04, 1.05, 0.43`

The three numbers represent:

- First value: average load during the last 1 minute
- Second value: average load during the last 5 minutes
- Third value: average load during the last 15 minutes

Load Average represents processes that were running or waiting for CPU or other system resources.

The node has 8 CPUs.

A simple way to read the value is to compare the Load Average with the number of CPUs.

For example:

- Load below 8: not automatically a CPU saturation problem
- Load around 8: CPU resources may be heavily used
- Load above 8: processes may be waiting for CPU resources

The actual CPU usage must also be checked because Load Average is not the same as CPU percentage.

In this lab:

- Load Average: 2.04
- CPUs: 8
- CPU Usage: 14%

Therefore, there was no clear CPU saturation on the node.

---

## Understanding Swap

Swap is disk space that Linux can use when available RAM becomes limited.

The basic idea is:

`RAM → Memory pressure → Swap`

RAM is much faster than disk storage, so heavy Swap usage can cause performance problems.

In this lab:

- RAM Usage: 29.04%
- Swap Usage: N/A

The node had enough available RAM, so there was no visible memory pressure.

A high Swap usage together with high RAM usage can be a warning sign that the node needs memory investigation.

---

## Storage Performance

Open:

`VM 100 → Summary`

Check:

- Disk Read
- Disk Write
- IO Delay

IO Delay is important when investigating storage performance.

A high IO Delay can indicate that the VM is waiting for storage operations.

In this lab:

`IO Delay = 0%`

Therefore, there was no visible storage I/O bottleneck at the time of the check.

---

## Performance Troubleshooting Method

When a user reports that a VM is slow, do not immediately change VM settings.

Use the following process:

1. Open the VM Summary.
2. Check CPU Usage.
3. Check Memory Usage.
4. Check Disk Read and Disk Write.
5. Check IO Delay.
6. Check the Proxmox node Summary.
7. Check Node CPU usage.
8. Check Node RAM usage.
9. Check Swap usage.
10. Check Load Average.
11. Review Task History.
12. Compare the current values with the normal baseline.

The goal is to identify the possible bottleneck before making changes.

---

## Task History

Open:

`Node → Task History`

Review recent operations such as:

- VM Start
- VM Stop
- Migration
- Backup
- Replication
- Storage operations

Task History helps the administrator connect performance problems with recent Proxmox operations.

For example, if a performance problem starts during a migration or storage operation, the task history can help identify what happened.
<img width="1577" height="635" alt="image" src="https://github.com/user-attachments/assets/d8e3249f-59ab-4e3e-a361-a498e920042c" />

---

## Performance Baseline

A baseline is a normal reference for the environment.

For this lab, the baseline included:

- Node CPU: 14%
- Node RAM: 29.04%
- Load Average: 2.04 / 1.05 / 0.43
- Node CPUs: 8
- Node Swap: N/A
- VM CPU: 100.19% of 1 vCPU
- VM RAM: 39.24 MiB of 2 GiB
- VM IO Delay: 0%

A single measurement is not enough to confirm a performance problem.

The administrator should compare values over time and look for unusual changes.

---

## Important Monitoring Rules

Do not troubleshoot a VM by looking at only one metric.

Always compare:

`CPU + Memory + Swap + Load Average + Storage I/O`

For example:

- High CPU + high Load Average → investigate CPU usage
- High RAM + high Swap → investigate memory pressure
- High IO Delay → investigate storage performance
- Normal VM resources + normal Node resources → investigate other causes

---

## What I Learned

- How to monitor a Proxmox node.
- How to monitor a virtual machine.
- How to read CPU and memory usage.
- How to understand Load Average.
- How to understand Swap.
- How to check storage I/O.
- How to use IO Delay during troubleshooting.
- How to use Task History.
- How to create a performance baseline.
- How to investigate a performance problem before changing configuration.

---

## Result

Task 12 was completed successfully.

The Proxmox node and VM were monitored using the Proxmox GUI.

The collected values did not show a clear CPU, memory, or storage bottleneck on the node.

The monitoring process can now be used as a baseline for future troubleshooting tasks.
