# Task 16 - Proxmox Backup Server

## Objective

Configure and use Proxmox Backup Server (PBS) with Proxmox VE.

The goal is to learn how to:

- Add PBS as backup storage.
- Create VM backups.
- Use incremental backup and deduplication.
- Verify backup integrity.
- Restore a VM from PBS.
- Configure backup retention with Prune.
- Understand Garbage Collection.
- Test backup and restore in a real lab scenario.

## Lab Environment

| Component | Configuration |
|---|---|
| Proxmox Node | proxmox-lab |
| PBS VM | pbs01 |
| PBS IP | 192.168.100.2 |
| PBS Port | 8007 |
| PBS Datastore | pds-datastore |
| Datastore Capacity | ~50 GB |
| PVE Storage ID | pbs01 |
| Backup VM | VM 100 |
| Restore Test VM | VM 107 |
| Restore Storage | d01 |

The PBS server was used from Node1 only.

No changes were made to `vmbr0` or `ens4` during this task.

## Scenario

The virtualization environment needs a dedicated backup server.

Instead of storing backups only on the Proxmox host, PBS is used to provide:

- Centralized backup storage.
- Incremental backups.
- Deduplication.
- Backup verification.
- Retention management.
- Restore testing.

## PBS Storage Configuration

A PBS virtual machine named `pbs01` was created.

The PBS datastore was created on the additional disk and configured as:

`pds-datastore`

The datastore was then added to Proxmox VE as PBS storage.

The storage status was verified as active.

Example storage status:

    pbs01  pbs  active

The datastore had approximately 50 GB available for the lab.
<img width="758" height="460" alt="image" src="https://github.com/user-attachments/assets/311b7218-e2c4-457b-a1e6-531ce0f62db4" />

## Backup VM 100

VM 100 was selected for the first PBS backup.

The backup was created successfully.

The backup log showed:

    Finished Backup of VM 100
    Backup job finished successfully
    TASK OK

The VM had a logical disk size of about 47 GiB.

However, PBS did not need to store the complete 47 GiB as new physical data.

The backup used:

- Sparse handling.
- Zero-data optimization.
- Incremental backup.
- Chunk reuse.
- Deduplication.

The backup log reported:

    backup is sparse: 43.31 GiB (92%) total zero data

and:

    backup was done incrementally, reused 43.63 GiB (92%)

This shows that PBS can avoid storing unnecessary zero data and can reuse chunks that already exist.
<img width="1820" height="1034" alt="image" src="https://github.com/user-attachments/assets/3a9bc655-bcd9-4a5c-8bad-d2d8e42f37bf" />

## Backup Verification

A Verification Job was configured for the PBS datastore.

The job was configured with:

- Schedule: Daily
- Skip Verified: Yes
- Re-Verify After: 30 Days

The verification completed successfully with:

    OK

### Verification Job Purpose

Verification does not create another backup.

It checks the integrity and readability of an existing backup.

The main difference is:

- Backup = creates the backup.
- Verify = checks the backup.
- Restore = uses the backup to rebuild the VM.

This provides an additional layer of protection against backup corruption.
<img width="746" height="463" alt="image" src="https://github.com/user-attachments/assets/0908efb5-d3ae-4bc8-829a-5bd13c30c868" />

## Restore Test

The VM 100 backup was restored as a new VM.

The first restore attempt used a Ceph storage target and failed because the Ceph storage operation timed out.

The PBS backup itself was not the problem.

The restore log showed that the PBS data restore completed successfully:

    restore image complete

The restore was then repeated using the local `d01` storage on Node1 instead of Ceph.

The restore completed successfully.

The restored VM received VM ID:

    107

VM 107 was started and booted successfully.

This confirmed that the PBS backup was not only created and verified, but could also be restored successfully.
<img width="1849" height="790" alt="image" src="https://github.com/user-attachments/assets/fe5b141f-0f0b-45d9-9976-7ee071bddaea" />

## Prune and Retention

A Prune configuration was tested on the PBS datastore.

The retention policy used:

    Keep Last: 1

This means PBS keeps only the latest backup snapshot according to the configured retention rule.

For example:

    Backup 1
    Backup 2

After Prune with Keep Last = 1:

    Backup 1 -> eligible for removal
    Backup 2 -> kept

Prune manages which backup snapshots should remain according to the retention policy.
<img width="1596" height="910" alt="image" src="https://github.com/user-attachments/assets/c57b2a50-aae0-49ce-a28e-e581bd810cc1" />
<img width="1351" height="742" alt="image" src="https://github.com/user-attachments/assets/2c125ce9-f62b-4b1c-9a1d-e20d3ead6231" />

## Garbage Collection

Garbage Collection (GC) is different from Prune.

Prune removes old backup snapshots from the retention set.

Garbage Collection then checks for data chunks that are no longer referenced by any remaining backup and removes those unused chunks.

The general process is:

    Backup
        |
        v
    Prune
        |
        v
    Old backup becomes unused
        |
        v
    Garbage Collection
        |
        v
    Unused chunks are removed
<img width="1844" height="982" alt="image" src="https://github.com/user-attachments/assets/dd3e7c5d-8994-4924-a5e4-877e4a0eb610" />

Prune does not always immediately return all disk space because chunks can still be shared by other backups.

## Backup Workflow

The complete PBS workflow tested in this lab was:

    VM
     |
     v
    Backup
     |
     v
    PBS Datastore
     |
     +----> Verify
     |
     +----> Restore
     |
     +----> Prune
     |
     +----> Garbage Collection

## Important Concepts

### Backup

Creates a recoverable copy of a VM.

### Incremental Backup

Transfers and stores data that is new or changed instead of storing everything again.

### Deduplication

PBS stores common chunks once and reuses them when possible.

### Verification

Checks that stored backup data can be read correctly.

### Restore

Rebuilds a VM from the backup.

### Prune

Applies the retention policy and removes backup snapshots that are no longer required.

### Garbage Collection

Removes unused backup chunks from the datastore.

## What I Learned

- How to add PBS to Proxmox VE.
- How to create VM backups using PBS.
- Why a 47 GiB VM disk can use much less physical PBS storage.
- How sparse and zero data optimization works.
- How incremental backup reuses existing chunks.
- How deduplication reduces storage usage.
- How Verification Jobs check backup integrity.
- How to restore a VM from PBS.
- The difference between Prune and Garbage Collection.
- How retention policies control the number of backup snapshots.
- Why restore testing is important for a backup system.

## Result

Task 16 was completed successfully.

The lab successfully demonstrated:

    PBS Storage        -> Working
    VM Backup          -> Successful
    Backup Verify      -> OK
    VM Restore         -> Successful
    Prune              -> Tested
    Retention          -> Keep Last = 1
    PBS Datastore      -> Active

The PBS backup and restore workflow was successfully tested on Node1 without changing the main Proxmox network.
