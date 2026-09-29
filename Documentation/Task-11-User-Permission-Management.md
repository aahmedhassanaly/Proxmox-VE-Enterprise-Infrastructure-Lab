# Task 11 — Proxmox User & Permission Management

## Objective

Learn how to manage users, groups, roles, permissions, and ACLs in Proxmox VE.

The goal is to give administrators and operators only the access they need.

---

## Lab Environment

- Proxmox Cluster: `proxmox-lab`
- Node 1: `proxmox-lab`
- Node 2: `proxmox-node2`
- Test VM: `VM 100`

---

## Users

Created two Proxmox users:

- `admin01@pve`
- `operator01@pve`

The `pve` realm uses the Proxmox VE authentication server.

---

## Groups

Created two groups:

- `proxmox-admins`
- `proxmox-operators`

Group membership:

- `proxmox-admins`
  - `admin01@pve`
- `proxmox-operators`
  - `operator01@pve`
<img width="1919" height="660" alt="image" src="https://github.com/user-attachments/assets/7fa56d32-9f41-4157-b90e-8932bb7b4554" />

---

## Roles and Permissions

### Administrator

The `proxmox-admins` group was assigned:

- Path: `/`
- Role: `PVEAdmin`
- Propagate: Yes

This gives the group administrative access at the Datacenter level.

### Operator

The `proxmox-operators` group was assigned access to VM 100:

- Resource: VM 100
- Role: `PVEVMUser`

This limits the operator's access to the assigned VM instead of giving access to the whole cluster.
<img width="1919" height="988" alt="image" src="https://github.com/user-attachments/assets/05ee32c9-f00e-4e53-9835-a17968be559b" />

---

## ACL Concept

Proxmox permissions can be understood as:

    User
      ↓
    Group
      ↓
    Role
      ↓
    Permission
      ↓
    Resource

Example:

    operator01@pve
          ↓
    proxmox-operators
          ↓
    PVEVMUser
          ↓
    VM 100

ACL means Access Control List. It defines who can access a resource and which role they have on that resource.

---

## Permission Testing

Logged in using:

    operator01@pve

The operator could see VM 100 only.

Other VMs were not visible to the operator.

This confirmed that the VM-level permission was working correctly.

The test demonstrated the principle of least privilege: users should receive only the access required for their job.
<img width="1915" height="938" alt="image" src="https://github.com/user-attachments/assets/1a864d4d-2df3-484e-be29-291c06c760cc" />

---

## Proxmox Authentication Realms

Two important realms were reviewed.

### Proxmox VE Authentication Server

Example:

    admin01@pve
    operator01@pve

These users are managed inside Proxmox VE.

### Linux PAM

Example:

    root@pam

This uses the Linux system authentication mechanism on the Proxmox host.

Simple difference:

    user@pve
        → Proxmox VE user

    user@pam
        → Linux/PAM user

Proxmox can also use external authentication systems such as LDAP or Active Directory, but they were not configured in this task.

---

## Important Roles

Some common built-in Proxmox roles include:

- `PVEAdmin` — broad Proxmox administration
- `PVEVMAdmin` — VM administration
- `PVEVMUser` — limited VM access
- `PVEAuditor` — read-only access

The exact permissions provided by each role should be checked before assigning it in a production environment.

---

## What I Learned

- How to create Proxmox users.
- How to create groups.
- How to add users to groups.
- How Roles control permissions.
- How to assign permissions at Datacenter or VM level.
- How ACLs control access to Proxmox resources.
- How to test permissions with a non-admin account.
- The difference between `@pve` and `@pam`.
- The importance of least-privilege access.

---

## Result

Task 11 was completed successfully.

Users and groups were created, permissions were assigned, and the operator account was tested successfully.

`operator01@pve` could access VM 100 without seeing the other VMs in the cluster.

This demonstrates practical Proxmox user and permission management for an enterprise environment.
