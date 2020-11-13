[TOC]

# Snapshots #
Snapshots are stored locally on the host computer on a given instance.

The snapshots are stored for containers at: 

* `/var/snap/lxd/common/lxd/storage-pools/STORAGE_POOL_NAME/containers-snapshots/` 

and for VMs:

* `/var/snap/lxd/common/lxd/storage-pools/STORAGE_POOL_NAME/virtual-machine-snapshots/`

with `STORAGE_POOL_NAME` being the storage pool used when creating the instance (usually `default` unless stated otherwise during or after instance creation).

## Creating a snapshot##
To create a snapshot of an existing instance, it is common practice to first stop its execution:
```
lxc stop instance
```

After stopping the container we can then create the snapshot:
```
lxc snapshot instance snapshot-name
```

To check the snapshots of an instance:
```
# The snapshots should appear at the bottom
lxc info instance
```

## Restoring a snapshot ##
To restore to a given snapshot, we can check the snapshot name to restore to as previously stated and then run the following command:
```
lxc restore instance snapshot-name
```

# Backups #
Exporting an image's instance allows us to store backups externally in case the host machine fails.


Both Container and VM images are stored at:

* `/var/snap/lxd/common/lxd/storage-pools/STORAGE_POOL_NAME/images/`

with `STORAGE_POOL_NAME` being in the exact same scenario as the snapshots.

## Creating a backup ##
Before creating a backup we must first create an image of our instance to be backed up:
```
lxc publish instance --alias instance-backup
```

We can check the instance image with `lxc image list`, it should look like the following:
```
+-----------------+--------------+--------+---------------------------------------------+--------------+-----------------+----------+-------------------------------+
|      ALIAS      | FINGERPRINT  | PUBLIC |                 DESCRIPTION                 | ARCHITECTURE |      TYPE       |   SIZE   |          UPLOAD DATE          |
+-----------------+--------------+--------+---------------------------------------------+--------------+-----------------+----------+-------------------------------+
| instance-backup | 2e70d21aeed0 | no     | Ubuntu 20.04 LTS server (20200907)          | x86_64       | CONTAINER       | 398.57MB | Sep 14, 2020 at 1:18pm (UTC)  |
+-----------------+--------------+--------+---------------------------------------------+--------------+-----------------+----------+-------------------------------+

```

With the image created we can now create a tar archive with it using `export`:
```
# This command will create an archive which can be stored externally
lxc image export instance-backup .
```

## Restoring a backup ##
To import a backup execute the following:
```
lxc image import ARCHIVE_NAME.tar.gz --alias=backup
```
**Note**: if attempting to import to the same machine for testing purposes, the previously published image must first be deleted, otherwise there will be a fingerprint conlfict.