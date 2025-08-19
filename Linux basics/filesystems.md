
# 1. Filesystem Hierarchy

Linux systems follow a standard directory structure called the **Filesystem Hierarchy Standard (FHS)**.  
You can view the directories under root by running:
```bash
$ ls -l /
```
### Key Directories

- `/` - The root directory of the entire filesystem hierarchy, everything is nestled under this directory.
- `/bin` - Essential ready-to-run programs (binaries), includes the most basic commands such as ls and cp.
- `/boot` - Contains kernel boot loader files.
- `/dev` - Device files.
- `/etc` - Core system configuration directory, should hold only configuration files and not any binaries.
- `/home` - Personal directories for users, holds your documents, files, settings, etc.
- `/lib` - Holds library files that binaries can use.
- `/media` - Used as an attachment point for removable media like USB drives.
- `/mnt` - Temporarily mounted filesystems.
- `/opt` - Optional application software packages.
- `/proc` - Information about currently running processes.
- `/root` - The root user's home directory.
- `/run` - Information about the running system since the last boot.
- `/sbin` - Contains essential system binaries, usually can only be ran by root.
- `/srv` - Site-specific data which are served by the system.
- `/tmp` - Storage for temporary files
- `/usr` - This is unfortunately named, most often it does not contain user files in the sense of a home folder. This is meant for user installed software and utilities, however that is not to say you can't add personal directories in there. Inside this directory are sub-directories for /usr/bin, /usr/local, etc.
- `/var` - Variable directory, it's used for system logging, user tracking, caches, etc. Basically anything that is subject to change all the time.

---
# 2. Filesystem Types

Linux supports **many different filesystem types**.  
Different filesystems have different performance, capacity, and data organization methods.  

Applications interact with filesystems through the **Virtual File System (VFS)** layer:  
- VFS is an abstraction between applications and the underlying filesystem.  
- It allows programs to work with different filesystems transparently.

#### Journaling

- Journaling helps maintain **filesystem consistency**.  
- Without journaling:  
  - If a crash happens while copying a large file, the file could be corrupted.  
  - The system must check the entire filesystem on reboot (`fsck`), which can take a long time.  
- With journaling:  
  - Operations are first logged in a **journal** before executing.  
  - If a crash happens, the filesystem knows what operations were incomplete.  
  - This **reduces boot time** and avoids corruption.  



#### Common Desktop Filesystem Types

| Filesystem | Description |
|------------|-------------|
| `ext4` | Most common Linux filesystem. Supports disks up to 1 exabyte and files up to 16 TB. Backward compatible with ext2/ext3. |
| `Btrfs` |  "Better or Butter FS" , Modern Linux FS with snapshots, incremental backups, performance improvements. Not fully stable yet. |
| `XFS` | High-performance journaling FS, great for large files (e.g., media servers). |
| `NTFS` / `FAT` | Windows filesystems. |
| `HFS+` | Macintosh filesystem. |


#### Check filesystems on your machine

Use the `df -T` command to see filesystem types and usage:

```bash
 $ df -T

Filesystem     Type     1K-blocks    Used Available Use% Mounted on
/dev/sda1      ext4       6461592 2402708   3707604  40% /
udev           devtmpfs    501356       4    501352   1% /dev
tmpfs          tmpfs       102544    1068    101476   2% /run
/dev/sda6      xfs       13752320  460112  13292208   4% /home
```

---
# 3. Anatomy of a Disk
Hard disks can be subdivided into partitions, essentially making multiple block devices. Recall such examples as, /dev/sda1 and /dev/sda2, /dev/sda is the whole disk, but /dev/sda1 is the first partition on that disk. Partitions are extremely useful for separating data and if you need a certain filesystem, you can easily create a partition instead of making the entire disk one filesystem type.

## Partition Table

Every disk will have a partition table, this table tells the system how the disk is partitioned. This table tells you where partitions begin and end, which partitions are bootable, what sectors of the disk are allocated to what partition, etc. There are two main partition table schemes used, Master Boot Record (MBR) and GUID Partition Table (GPT).

## Partition

Disks are comprised of partitions that help us organize our data. You can have multiple partitions on a disk and they can't overlap each other. If there is space that is not allocated to a partition, then it is known as free space. The types of partitions depend on your partition table. Inside a partition, you can have a filesystem or dedicate a partition to other things like swap (we'll get to that soon).

MBR

- Traditional partition table, was used as the standard
- Can have primary, extended, and logical partitions
- MBR has a limit of four primary partitions
- Additional partitions can be made by making a primary partition into an extended partition (there can only be one extended partition on a disk). Then inside the extended partition you add logical partitions. The logical partitions are used just like any other partition. Silly I know.
- Supports disks up to 2 terabytes

GPT
- GUID Partition Table (GPT) is becoming the new standard for disk partitioning
- Has only one type of partition and you can make many of them
- Each partition has a globally unique ID (GUID)
- Used mostly in conjunction with UEFI based booting (we'll get into details in another course)

## Filesystem Structure

- Boot block - This is located in the first few sectors of the filesystem, and it's not really used the by the filesystem. Rather, it contains information used to boot the operating system. Only one boot block is needed by the operating system. If you have multiple partitions, they will have boot blocks, but many of them are unused.
- Super block - This is a single block that comes after the boot block, and it contains information about the filesystem, such as the size of the inode table, size of the logical blocks and the size of the filesystem.
- Inode table - Think of this as the database that manages our files (we have a whole lesson on inodes, so don't worry). Each file or directory has a unique entry in the inode table and it has various information about the file.
- Data blocks - This is the actual data for the files and directories.

Examples:
MBR
```bash
varun@Avya:/home/varun:~$ sudo parted -l

Model: Seagate (scsi)
Disk /dev/sda: 21.5GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos

Number  Start   End     Size    Type      File system     Flags
 1      1049kB  6860MB  6859MB  primary   ext4            boot
 2      6861MB  21.5GB  14.6GB  extended
 5      6861MB  7380MB  519MB   logical   linux-swap(v1)
 6      7381MB  21.5GB  14.1GB  logical   xfs


```

GPT
```bash
Model: Thumb Drive (scsi)
Disk /dev/sdb: 4041MB
Sector size (logical/physical): 512B/512B
Partition Table: gpt

Number  Start   End     Size     File system  Name        Flags
 1      17.4kB  1000MB  1000MB                first
 2      1000MB  4040MB  3040MB                second

```

---


# 4. Disk Partitioning

Disk partitioning allows you to **divide a physical disk into separate sections**, each of which can act like its own disk.  

You can practice on a **USB drive**, or just follow along without one.  

#### Tools for Partitioning

| Tool | Description |
|------|-------------|
| `fdisk` | Basic command-line partitioning tool. Only supports MBR. |
| `parted` | CLI tool that supports both MBR and GPT partitioning. |
| `gparted` | GUI version of `parted`. |
| `gdisk` | Like `fdisk`, but only supports GPT. |

#### Partitioning with `parted`

##### 1. Launch `parted`
```bash
 $ sudo parted
```
You will enter the parted interactive shell.

##### 2. Select your device
```bash
(parted) select /dev/sdb2
```
Replace /dev/sdb2 with the device you want to partition.

##### 3. View the current partition table
```bash
(parted) print
```
Examples output:

```bash

Model: Seagate (scsi)
Disk /dev/sda: 21.5GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos

Number  Start   End     Size    Type      File system     Flags
 1      1049kB  6860MB  6859MB  primary   ext4            boot
 2      6861MB  21.5GB  14.6GB  extended
 5      6861MB  7380MB  519MB   logical   linux-swap(v1)
 6      7381MB  21.5GB  14.1GB  logical   xfs

```
- Start/End: Defines where partitions begin and end.

- Type: Primary, extended, or logical.

- File system: Ext4, XFS, swap, etc.

##### 4. Create a new partition
```bash
(parted) mkpart primary 123 4567
```

- `primary` = partition type

- `123` = start point (MB)

- `4567` = end point (MB)
##### 5. Resize a partition
```bash
(parted) resize 2 1245 3456
```

- `2` = partition number

- `1245` = new start (MB)

- `3456` = new end (MB)

---
# 5. Creating Filesystems

After partitioning a disk, the next step is to **create a filesystem** on it.  

## Create a filesystem with `mkfs`

```bash
 $ sudo mkfs -t ext4 /dev/sdb2
```
- `mkfs` = make filesystem

- `-t ext4` = specify the filesystem type (e.g., ext4, xfs, btrfs)

- `/dev/sdb2` = the partition to create the filesystem on

---

# 6. mount and umount

Before you can access a filesystem, you must **mount** it to a directory (mount point) in your system.  


## Steps to Mount a Filesystem

1. **Create a mount point**
```bash
 $ sudo mkdir /mydrive
```

2. **Mount the device**
```bash
 $ sudo mount -t ext4 /dev/sdb2 /mydrive

```

- `-t ext4` = filesystem type

- `/dev/sdb2` = device location

- `/mydrive` = directory where the filesystem will be attached

Now, you can access the filesystem at `/mydrive`.

3. **Unmounting a Filesystem**
```bash

$ sudo umount /mydrive 

or 

$ sudo umount /dev/sdb2
```

#### Using UUIDs for Mounting

Device names may change depending on the order the kernel finds them.
You can use a device's UUID (universally unique ID) instead of its name.

1. **Check UUIDs**
```bash
$ sudo blkid
```

Example output:
```bash
/dev/sda1: UUID="130b882f-7d79-436d-a096-1e594c92bb76" TYPE="ext4"
/dev/sda5: UUID="22c3d34b-467e-467c-b44d-f03803c2c526" TYPE="swap"
/dev/sda6: UUID="78d203a0-7c18-49bd-9e07-54f44cdb5726" TYPE="xfs"
```

2. **Mount using UUID**
```bash
sudo mount UUID=130b882f-7d79-436d-a096-1e594c92bb76 /mydrive
```

---
# 7. /etc/fstab

`/etc/fstab` (filesystem table) is used to **automatically mount filesystems at startup**.  


## Viewing `/etc/fstab`
```bash
$ cat /etc/fstab
```

Examples:
```bash
UUID=130b882f-7d79-436d-a096-1e594c92bb76 /               ext4    relatime,errors=remount-ro 0       1
UUID=78d203a0-7c18-49bd-9e07-54f44cdb5726 /home           xfs     relatime        0       2
UUID=22c3d34b-467e-467c-b44d-f03803c2c526 none            swap    sw              0       0
```

Each line represents one filesystem, the fields are:

- UUID - Device identifier
- Mount point - Directory the filesystem is mounted to
- Filesystem type
- Options - other mount options, see manpage for more details
- Dump - used by the dump utility to decide when to make a backup, you should just default to 0
- Pass - Used by fsck to decide what order filesystems should be checked, if the value is 0, it will not be checked

To add an entry, just directly modify the /etc/fstab file using the entry syntax above. Be careful when modifying this file, you could potentially make your life a little harder if you mess up.


---
# 8. Swap
Well swap is what we used to allocate virtual memory to our system. If you are low on memory, the system uses this partition to "swap" pieces of memory of idle processes to the disk, so you're not bogged for memory.

### Using a partition for swap space

1. First make sure we don't have anything on the partition
2. Run: `mkswap /dev/sdb2` to initialize swap areas
3. Run: `swapon /dev/sdb2` this will enable the swap device
4. If you want the swap partition to persist on bootup, you need to add an entry to the /etc/fstab file. sw is the filesystem type that you'll use.
5. To remove swap: `swapoff /dev/sdb2`

---
# 9. Disk Usage

There are a few tools to check **disk utilization**:

---

## `df` – Disk Free
Shows the **used and available space** on mounted filesystems.

```bash
varun@Avya:/home/varun$ df -h
Filesystem     1K-blocks    Used Available Use% Mounted on
/dev/sda1       6.2G       2.3G    3.6G   40% /
```
- `-h` = human-readable (e.g., GB, MB)

- Use `df` when you want to see free space on di
sks.

## `du` - Disk Usage
Shows the disk usage of files and directories.
```bash
$ du -h
```
- `-h` = human-readable

- Use `du -h` / to check usage of the entire root, but output can be long.

- Use `du` to see which directories or files are taking up space.

---
# 10. Filesystem Repair
The fsck (filesystem check) command is used to check the consistency of a filesystem and can even try to repair it for us. Usually when you boot up a disk, fsck will run before your disk is mounted to make sure everything is ok. Sometimes though, your disk is so bad that you'll need to manually do this. However, be sure to do this while you are in a rescue disk or somewhere where you can access your filesystem without it being mounted.
```bash
$ sudo fsck /dev/sda
```
---
# 11. Inodes
An inode (index node) is an entry in this table and there is one for every file. It describes everything about the file, such as:

- File type - regular file, directory, character device, etc
- Owner
- Group
- Access permissions
- Timestamps - mtime (time of last file modification), ctime (time of last attribute change), atime (time of last access)
- Number of hardlinks to the file
- Size of the file
- Number of blocks allocated to the file
- Pointers to the data blocks of the file - most important!
Basically inodes store everything about the file, except the filename and the file itself!

### When are inodes created?

When a filesystem is created, space for inodes is allocated as well. There are algorithms that take place to determine how much inode space you need depending on the volume of the disk and more. You've probably at some point in your life seen errors for out of disk space issues. Well the same can occur for inodes as well (although less common), you can run out of inodes and therefore be unable to create more files. Remember data storage depends on both the data and the database (inodes).

To see how many inodes are left on your system, use the command `df -i`

#### Inode information

Inodes are identified by numbers, when a file gets created it is assigned an inode number, the number is assigned in sequential order. However, you may sometimes notice when you create a new file, it gets an inode number that is lower than others, this is because once inodes are deleted, they can be reused by other files. To view inode numbers run `ls -li`:
```bash
varun@Avya:/home/varun:~$ ls -li

140 drwxr-xr-x 2 varun varun 6 Jan 20 20:13 Desktop

141 drwxr-xr-x 2 varun varun 6 Jan 20 20:01 Documents
```
The first field in this command lists the inode number.

You can also see detailed information about a file with stat, it tells you information about the inode as well.
```bash

varun@Avya:/home/varun:~$ stat ~/Desktop/
  File: ‘/home/pete/Desktop/’
  Size: 6               Blocks: 0          IO Block: 4096   directory
Device: 806h/2054d      Inode: 140         Links: 2
Access: (0755/drwxr-xr-x)  Uid: ( 1000/   pete)   Gid: ( 1000/   pete)
Access: 2016-01-20 20:13:50.647435982 -0800
Modify: 2016-01-20 20:13:06.191675843 -0800
Change: 2016-01-20 20:13:06.191675843 -0800
Birth: -

```


### How Inodes Locate Files

* **Inodes** store metadata about files (permissions, owner, timestamps) and **pointers to the actual data blocks** on disk.
* Data blocks of a file may **not be stored sequentially**, so inodes are used to locate all the blocks.
* **Pointer structure in a typical inode (15 pointers):**

  1. **First 12 pointers** → point directly to data blocks (direct blocks).
  2. **13th pointer** → points to a block that contains more pointers (single indirect).
  3. **14th pointer** → points to a block that points to other blocks of pointers (double indirect).
  4. **15th pointer** → points to a block that points to blocks of pointers of pointers (triple indirect).
---

# 12. Symlinks and Hardlinks

## Symlinks (Symbolic Links)

- Similar to **shortcuts in Windows**.  
- Symlinks point to a **filename** rather than an inode.  
- Can span **different filesystems**.  
- Denoted by `->` when using `ls -l`.  

### Example
```bash
varun@Avya:/home/varun $ echo 'myfile' > myfile
varun@Avya:/home/varun $ ln -s myfile mylink
varun@Avya:/home/varun $ ls -li
```
Example output:
```bash
151 -rw-rw-r-- 1 varun varun 7 Jan 21 21:36 myfile
93403 lrwxrwxrwx 1 varun varun 6 Jan 21 21:39 mylink -> myfile
```
- Modifying the symlink modifies the target file.

- Has its own inode.

- Can reference files across different filesystems.

### Command to create a symlink
```bash
$ ln -s target_file link_name
```
Example
```bash
$ ln -s myfile mylink
```
## Hardlinks
Another file pointing to the same inode as the original file.

- Changes to either file affect the other.

- Cannot span filesystems.

- The link count of the inode increases for each hardlink.

- File is only deleted when all hardlinks are removed.
