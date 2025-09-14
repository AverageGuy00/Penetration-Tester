# File systems

| File System |               Key Features               |          Use Case          |
| :---------: | :--------------------------------------: | :------------------------: |
|    ext2     |       No journaling, low overhead        | Legacy systems, USB drives |
|    ext3     |        Journaling, crash recovery        |    Older Linux systems     |
|    ext4     | Journaling, large files support, default | Most modern Linux systems  |
|    Btrfs    |        Snapshots, data integrity         |   Complex storage setups   |
|     XFS     |      High performance, large files       |   High I/O environments    |
|    NTFS     |          Windows compatibility           | Dual-boot, external drives |

# Key Concepts

|   Concept   |                                    Description                                    |
| :---------: | :-------------------------------------------------------------------------------: |
|   Inodes    |  Store file metadata (permissions, owner, size, timestamps), point to file data   |
| Inode Table |                 Collection of inodes; OS tracks files using this                  |
| File Types  | Regular files (text/binary), Directories (containers), Symbolic links (shortcuts) |
| Permissions |           Read, write, execute per user category (owner, group, others)           |

# Disk Management

|           Task            |           Command / Tool            |           Description           |
| :-----------------------: | :---------------------------------: | :-----------------------------: |
|      List partitions      |           `sudo fdisk -l`           |      Shows partition info       |
| Create/manage partitions  |     `fdisk`, `gpart`, `GParted`     |        Partition drives         |
|        Mount drive        | `sudo mount /dev/sdXn /mount/point` |     Temporarily mount drive     |
| List mounted file systems |               `mount`               |     Show all mounted drives     |
|       Unmount drive       |     `sudo umount /mount/point`      |      Unmount drive safely       |
|     Automount at boot     |          Edit `/etc/fstab`          | Define mount points and options |

# Swap Management

|       Task        |      Command       |                        Description                        |
| :---------------: | :----------------: | :-------------------------------------------------------: |
| Create swap space | `mkswap /dev/sdXn` |              Prepare partition/file as swap               |
|    Enable swap    | `swapon /dev/sdXn` |                   Activate swap for use                   |
|    Swap usage     |        N/A         |    Extends RAM, supports hibernation, can be encrypted    |
|    Swap sizing    |        N/A         | Depends on RAM and workload; modern systems may need less |

# Mounted Tips

|       Tip       |           Description           |               Example                |
| :-------------: | :-----------------------------: | :----------------------------------: |
|    Use UUIDs    | More reliable than device names | `UUID=xxxx-xxxx / ext4 defaults 0 0` |
| Temporary mount |    Mount a drive temporarily    |   `sudo mount /dev/sdb1 /mnt/usb`    |
|   Check mount   |   Verify mounted file systems   |               `mount`                |
