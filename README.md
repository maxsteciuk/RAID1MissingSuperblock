# RAID-1 Missing Superblock
Instructions how to recover RAID-1 array from the missing superblock error

**CAUTION Please ensure you have additional backup of your data prior to continuing the recovery**

On Ubuntu Linux I have RAID-1 set up for various data backups. One day when I attempted to boot to Ubuntu I failed to do so. After some preliminary troubleshooting it was found that most of RAID-1 array members could not be mounted. When inspected further the **mdadm** tool, a tool used to create RAID-1 array, showed that superblocks for affected RAID array members were missing.

The following page describes in detail how to recover a RAID array in similar circumstances: [Irreversible mdadm failure recovery](https://raid.wiki.kernel.org/index.php/Irreversible_mdadm_failure_recovery)
The command that resolved this particular problem was:
```
mdadm --create /dev/md/vol<RAID array member #> --level=1 --chunk=512K --metadata=1.2  --data-offset=262144s   --raid-devices=2 /dev/sd<disk 1 of RAID-1> /dev/sd<disk 2 of RAID-1> --assume-clean --readonly
```

## Notes:
- As a result the RAID partitions have been mounted in read-only mode which is at least sufficient to ensure data is intact.
- One suspicion about what could cause such the error is that after the latest update the Ubuntu **fsck**  was erroneously run against RAID array. To ensure **fsck** is not run against RAID array the **Pass** column in the **/etc/fstab** configuration file needs to be set to 0 as follows.
```
# Device                Mountpoint              FStype          Options Dump Pass
UUID of disk      mountpoint            ext4             rw              0       0

```
