
# Module 1: Linux Fundamentals

- Linux Introduction
- Linux Architecture
- Linux Installation
- Shell Basics
- Linux Basic Symbols
- Options / Switches

# Module 2: Linux Boot Process

- Linux Boot Process
- Reset Root Password

# Module 3: Linux File System

- FHS (Directory Structure)
- File Types & Inodes
- Filesystem Concepts
- Mount Points & Mounting
- Hard & Soft Links

# Module 4: Linux Commands

- Basic Commands
- File Management
- VI Editor
- Text Processing
- Searching
- Compression
- Redirection & Pipes
- Command Filters
- Copy & Move

# Module 5: User Administration

- Users & Groups
- `/etc/passwd` & `/etc/shadow`
- Password Management
- Permissions
- `chmod`, `chown`, `chgrp`
- ACL
- SUID, SGID, Sticky Bit
- Hard & Soft Links

# Module 6: Process & Package Management

- Process Management
- Job Control
- Signals
- RPM/YUM/DNF/APT

# Module 7: Service Management

- systemd
- Services & Daemons
- Targets
- Runlevels
- Journald

# Module 8: Storage Management

- Disk & Partition Concepts
  - Disk vs Partition
  - Primary / Extended / Logical Partitions
  - Partition Tables: MBR & GPT

- Partitioning
  - Standard Partition
  - Swap
  - RAID
  - LVM
  - Partitioning Tools: fdisk, gdisk, parted

# Module 9: Networking

- Networking Basics
- IP Configuration
- DNS
- SSH
- Firewall
- Network Troubleshooting

# Module 10: Server Administration

- Logs
- NFS
- Samba
- Apache
- NGINX
- FTP

# Module 11: Backup & Recovery

- Backup Concepts (Full, Incremental, Differential)
- `tar` for Backup & Restore
- `rsync`
- `cpio`
- `dd` (Disk/Partition Backup)
- Snapshot Basics (LVM Snapshot)
- Backup Verification
- Backup Scheduling using Cron
- Restore Files and Directories
- Disaster Recovery Basics

# Module 12: Automation

- Cron Jobs
- At Jobs
- Bash Shell Scripting

# Module 13: Security

- SELinux
- AppArmor
- sudo & Password Policies
- SSH Hardening

# Module 14: Monitoring & Performance

- Log Analysis
- System Monitoring
- Performance Monitoring
- Resource Management

# Module 15: Virtualization & Containers

- VMware/KVM
- Docker
- Podman
- Cloud Linux (AWS)

# Module 16: Linux Troubleshooting

- Boot Issues
- Disk & Filesystem Issues
- Network Issues
- Service Failures
- Performance Issues
- Permission Issues

# Module 17: Interview Preparation

- Linux Interview Questions
- Real-Time Scenarios
- Command Practice
- Bash Scripting Practice
----
<br>
<br>
<br>
<br>
<br>


 # Module 2: Linux Boot Process

## What is the Booting process of Linux?

The booting process is the sequence of steps that your Linux system follows to start, from the moment you power it on until you reach the login prompt or desktop.

There are 6 stages of the Linux Boot Process.

## 1. BIOS / UEFI – Power-On Self Test (POST)

(Think of BIOS/UEFI as the Security Guard.)

⟶ When we power on the system, the BIOS (Basic Input Output System) or UEFI performs a hardware check (POST)

⟶ Its job is simply to check whether the computer is healthy. It checks hardware such as RAM, CPU, keyboard, and disks.

⟶ After POST, BIOS/UEFI looks for a bootable device (like HDD, SSD, or USB).

Then it loads the first sector (512 bytes) of the bootable disk—the MBR (Master Boot Record) or the EFI partition.

## 2. MBR / GPT

This is the Boot Loader Stage 1.

MBR (Master Boot Record) or GPT (GUID Partition Table) contains:

⟶ The boot loader information.

⟶ The partition table.

MBR loads the bootloader’s first stage (like GRUB)

Location:

⟶ MBR is stored in the first 512 bytes of the Boot disk.

⟶ Contains small boot code + partition details.

{BIOS reads the MBR and executes its boot code.

The MBR's job is not to boot Linux directly.

Its only job is to find and start the bootloader (GRUB)}

## 3. GRUB (Grand Unified Bootloader)

This is the Boot Loader stage 2.

GRUB does:

⟶ Displays a boot menu (if multiple OS exist).

⟶ Loads the Linux kernel (vmlinuz-<version>)

⟶ Loads the initramfs/initrd (initial RAM disk)

GRUB config files:

/boot/grub2/grub.cfg ⟶ (for RHEL/CentOS)

/boot/grub/grub.cfg ⟶ (for Ubuntu/Debian)

### In Simple Words:-

(Think of GRUB as the Manager.)

{GRUB asks:

"Which operating system do you want to start?"

Example:

- Ubuntu
- Windows
- Recovery Mode

You choose Ubuntu.

GRUB loads:

- Linux Kernel
- initramfs

into RAM.
}

## 4. Kernel Initialization

Once GRUB loads the kernel and initramfs, control passes to the Linux kernel.

⟶ Kernel initialized hardware drivers.

⟶ Mounts the root filesystem (/).

⟶ Starts the first process (PID 1 → systemd).

Initramfs helps the kernel load necessary drivers (for disks, filesystems, etc.) before the real root filesystem becomes available.

### In simple words:-

Linux Kernel (CEO)

The Kernel takes control.

It initializes:

- CPU
- Memory
- USB
- Keyboard
- Device drivers

Since the real filesystem isn't available yet, it temporarily uses **initramfs** (Temporary Toolbox).

Initramfs contains the drivers and files needed to locate and mount the real root filesystem.

Once the root filesystem is mounted, initramfs is discarded.

## 5. Systemd (or init) – User Space Initialization

After the kernel mounts the root filesystem, it runs /sbin/init (or systemd in modern distros).

Systemd is the first user-space process (PID 1)

Responsibilities:

⟶ Systemd starts all other background services (daemons).

Examples:

- Network
- SSH
- Bluetooth
- Printer
- Audio
- Display Manager

⟶ Mounts file systems.

⟶ Brings the system to the desired target (runlevel).

## 6. Login (User Space) / Login Screen

Finally:

⟶ The system starts with login prompts (getty on terminals or GDM for GUI).

⟶ You log in via username/password.

Username:

Password:

⟶ Shell (bash, zsh, etc.) has started.
## Complete Flow (Legacy BIOS)

```text
Power Button
      ↓
BIOS
      ↓
POST
      ↓
Read MBR
      ↓
GRUB (Bootloader)
      ↓
Load Kernel + initramfs
      ↓
Kernel Starts
      ↓
Mount Root Filesystem (/)
      ↓
systemd (PID 1)
      ↓
Services Start
      ↓
Login Screen
      ↓
Desktop / Shell
```

### Check boot logs

```bash
dmesg | less
```

(Dmesg stands for display message)

### Q2. Why use `dmesg | less` instead of just `dmesg`?

**Answer:**

Because `dmesg` can produce hundreds of lines of output. Piping it to `less` lets you read and search the output page by page.

### Check the last boot time

```bash
who -b
```

### See system boot duration

```bash
systemd-analyze
```

### See which services took the longest during boot

```bash
systemd-analyze blame
```

## Linux Boot Process (In Short)

### 1. BIOS/UEFI

⟶ BIOS initializes hardware components.

⟶ Performs POST (Power On Self-Test) to check RAM, CPU, and storage device.

⟶ Loads the bootloader (GRUB) into memory.

### 2. Bootloader (GRUB) Grand Unified Bootloader

⟶ Loads the Linux kernel into memory.

⟶ Provides a menu to select a different kernel or operating system.

### 3. Kernel Initialization

⟶ Linux kernel loaded into RAM.

⟶ Initializes hardware drivers and memory management.

⟶ Mounts the initial root filesystem (initramfs and initrd).

⟶ Starts the init system (systemd).

### 4. Init System (systemd)

⟶ systemd starts essential services and daemons.

⟶ Mounts file systems (`/`, `/home`, `/var`) etc.

⟶ Switches the system to the target runlevel (`default.target`).

### 5. Runlevel/Target Initialization

⟶ systemd uses targets.

⟶ Common targets:

- `multi-user.target`
- `graphical.target`

### 6. Login Prompt

⟶ The system presents a login prompt.

---
<br>
<br>

# Module 3: Linux File System

## 3.1.) FHS (Directory Structure)

### 1. What is a File System?

*A file system is the method Linux uses to store, organize, and retrieve data on a storage device (HDD, SSD, USB, etc.).*

Think of it like a library.

- The hard disk is the library building.
- Directories are shelves.
- Files are books.
- The file system is the catalog that tells Linux where every book is located.

### 2. What is FHS?

FHS (Filesystem Hierarchy Standard) is a standard that defines where files and directories should be stored in Linux.

Instead of every Linux distribution storing files differently, FHS provides a common structure.

For example:

- Configuration files are stored in **/etc**
- User home directories are stored in **/home**
- Log files are stored in **/var/log**
- Boot files are stored in **/boot**

## Linux Directory Structure

Everything on Linux starts from a single directory called the **root directory**.

Unlike Windows, Linux doesn’t use drive letters such as C: / D:.

Everything begins from `/`.

### Linux File System

```text
(/) Root

├ System  → /boot, /bin, /sbin, /lib, /etc, /usr
├ Users   → /home, /root
├ Storage → /tmp, /mnt, /media
└ Runtime → /dev, /proc, /sys, /run, /var, /opt, /srv
```

### 1.) /boot

- Contains files required to boot Linux.
- Examples:
  - Linux Kernel (vmlinuz)
  - initramfs
  - GRUB files
  - Boot configuration

### 2.) /bin (User commands)

- Meaning: Binary executables.
- Contains essential commands required by all users.
- Examples:
  - ls
  - cp
  - mv
  - cat
  - pwd
  - mkdir
  - rm
  - echo

### 3.) /sbin (Admin commands)

- Mostly used by the root user.
- Usually requires administrative privileges.
- On many modern systems, `/sbin` is linked to `/usr/sbin`.
- Examples:
  - fdisk
  - reboot
  - shutdown
  - mkfs
  - fsck
  - ip

### 4.) /lib (Libraries)

- Contains shared libraries needed by programs and the kernel.
- `/lib` and `/lib64` contain essential libraries needed during boot and for basic system commands.
- `/usr/lib` and `/usr/lib64` contain additional libraries for installed applications.

### 5.) /etc (Configuration files)

- Contains system-wide configuration files.

Example:

```text
/etc/passwd
/etc/shadow
/etc/fstab
/etc/hosts
/etc/resolv.conf
```

### 6.) /usr (Applications)

- Contains user applications and shared resources.

Examples:

```text
/usr/bin
/usr/sbin
/usr/lib
/usr/share
```

### 7.) /home (Normal users)

- Contains home directories of normal users.

Example:

```text
/home/user1
```

### 8.) /root (Root user's home)

- Home directory of the **root user**.

### 9.) /tmp

- Stores temporary files.
- Usually cleared after reboot.
- Any user can write here.

### 10.) /mnt

- Temporary mount point used by administrators.

Example:

```bash
mount /dev/sdb1 /mnt
```

### 11.) /media

- Used for automatically mounted removable devices.

Examples:

- USB Drive
- CD/DVD
- External HDD

### 12.) /dev

- Contains device files.
- In Linux, every hardware device is represented as a file.

Examples:

```text
/dev/sda
/dev/sdb
/dev/null
```

### 13.) /proc

- A virtual filesystem.
- It doesn't store files on disk.
- Instead, it provides live information about:
  - CPU
  - Memory
  - Running processes

Examples:

```bash
cat /proc/cpuinfo
cat /proc/meminfo
```

### 14.) /sys

- Another virtual filesystem.
- Provides information about:
  - Hardware
  - Kernel
  - Drivers

### 15.) /run

- Stores runtime information.

Examples:

- PID files
- Sockets
- Runtime service information

- Contents are usually cleared after reboot.

### 16.) /var

- Stores variable data.

Examples:

- Logs
- Mail
- Cache
- Databases
- Print queues

Important directory:

```text
/var/log
```

### 17.) /opt

- Used for optional or third-party software.

Example:

```text
/opt/google
```

### 18.) /srv

- Contains data served by services.

Examples:

- FTP server files
- Web server content
- Application data

 # 3.2.) File Types & Inodes

## What is a File in Linux?

In Linux, **everything is treated as a file**.

Not only documents and images, but also:

- Hard disks
- USB drives
- Keyboards
- Mouse
- Printers
- Running processes

## What are File Types?

Linux stores different kinds of files.

Every file belongs to one of **seven** file types.

## Seven Linux File Types

| Symbol | File Type | Description |
|----------|----------|----------|
| - | Regular File | Stores data (text, images, videos, scripts, etc.) |
| d | Directory | Contains files and folders |
| l | Symbolic (Soft) Link | Points to another file or directory |
| c | Character Device | Transfers data one character at a time (keyboard, terminal) |
| b | Block Device | Transfers data in blocks (HDD, SSD, USB) |
| p | Named Pipe (FIFO) | Used for communication between processes |
| s | Socket | Used for communication between applications |

## I. Regular File ( - )

- Stores actual user data.
- Example:
  - photo.jpg
  - resume.pdf

Command:

```bash
ls -l
```

Output:

```text
-rw-r--r--
```

## II. Directory ( d )

- A directory stores references to files and other directories.
- Think of it as a folder.

Command:

```bash
mkdir Linux
ls -l
```

Output:

```text
drwxr-xr-x
```

## III. Symbolic Link ( l )

- A symbolic link is like a shortcut in Windows.
- It points to another file or directory.

Create:

```bash
ln -s file.txt shortcut
```

Output:

```text
lrwxrwxrwx
```

## IV. Character Device ( c )

- Transfers data character by character.

Examples:

```text
/dev/tty
```

Check:

```bash
ls -l /dev/null
```

Output:

```text
crw-rw-rw-
```

## V. Block Device ( b )

- A block device is a device that stores data in fixed-size blocks (chunks) instead of one character at a time.

Examples:

- Hard Disk (HDD)
- SSD
- USB Drive

Linux device files:

```text
/dev/sda
/dev/sdb
```

- Transfers data in blocks.
- Used by storage devices.

Check:

```bash
ls -l /dev/sda
```

Output:

```text
brw-rw----
```

## VI. Named Pipe ( p )

- Used for communication between two processes.

Example:

Create:

```bash
mkfifo mypipe
```

Output:

```text
prw-r--r--
```

## VII. Socket ( s )

- Used for communication between applications.

Example:

```text
Web Server ↔ Database
```

Check:

```bash
find /run -type s
```

## Q) How to Identify File Types?

Answer:

```bash
ls -l
```

or

```bash
file filename
```

Output:

```text
-rw-r--r-- file.txt
drwxr-xr-x folder
lrwxrwxrwx shortcut
```

The first character identifies the file type.

# Inodes

An inode (Index Node) is a data structure that stores metadata (information) about a file.

Think of an inode as an **identity card** for a file.

> Important:
>
> An inode does not store the file's name or its actual data.
>
> It stores information about the file, while the data itself is stored in data blocks and the filename is stored in the parent directory entry.

## Q) Command to View Inode Number

```bash
ls -i
```

## Q) View Detailed Inode Information

```bash
stat filename
```

Shows:

- Inode number
- Permissions
- Owner
- Size
- Timestamps
- Link count

## Q) What Information Does an Inode Store?

An inode stores:

- File type
- File permissions
- Owner (UID)
- Group (GID)
- File size
- Last Access Time (atime)
- Last Modification Time (mtime)
- Last Status Change Time (ctime)
- Number of Hard Links
- Location of Data Blocks (pointers to where the file's contents are stored)

It **does not store**:

- File name
- Actual file data

## Q) How Linux Opens a File

```text
Directory
→ Find "report.txt"
→ Get Inode Number
→ Read the Inode
→ Find Data Blocks
→ Read File Contents
```

Linux follows these above steps.

Directories are important because they connect filenames to inode numbers.

## Q) What Happens in Different Operations?

### Copy (cp)

- New inode created
- New data blocks created

### Rename (mv) (within the same filesystem)

- File name changes
- Inode remains the same

### Delete (rm)

- Directory entry removed
- Link count decreases
- Inode and data blocks are freed when no hard links remain and no process is using the file

## Q) Check Inode Usage?

```bash
df -i
```

Useful when:

- Disk has free space
- Still unable to create files

Reason:

**Inodes are exhausted.**

# 3.3) Filesystem Concepts

Now we reach the concept that connects **Module 3** and **Module 8**.

## What is a Filesystem?

A filesystem is a **method/structure used by Linux to organize and manage data on storage**.

Examples of Linux filesystems:

```text
XFS
ext4
ext3
Btrfs
```

For example:

```text
/dev/sdb1
    ↓
   XFS
    ↓
 Mounted at /data
```

Here:

* `/dev/sdb1` → Partition/device providing storage
* `XFS` → Filesystem used to organize data
* `/data` → Mount point where the filesystem is made accessible

The **partition/device provides storage**, while the **filesystem determines how files and directories are organized on that storage**.

---

### Disk vs Partition vs Filesystem

This distinction is **extremely important** for a Linux Administrator.

Imagine:

```text
Physical Disk
/dev/sdb

│
├── /dev/sdb1
│      ↓
│     XFS
│
└── /dev/sdb2
       ↓
      ext4
```

Here:

```text
/dev/sdb    → Disk

/dev/sdb1   → Partition

/dev/sdb2   → Partition

XFS         → Filesystem

ext4        → Filesystem
```

We can then mount the filesystem:

```text
/dev/sdb1
    ↓
   XFS
    ↓
  mount
    ↓
  /data
```

After mounting, users and applications can access the files through:

```text
/data
```

### Simple way to remember

```text
Disk
  ↓
Partition
  ↓
Filesystem
  ↓
Mount Point
  ↓
Files & Directories
```

Example:

```text
/dev/sdb
   ↓
/dev/sdb1
   ↓
  XFS
   ↓
 /data
   ↓
file1
file2
logs/
```

This is the basic relationship between **storage, partitions, filesystems, and directories** in Linux.


# 3.4) Mount points & Mounting Filesystems
### Mount Point

The **directory** where the filesystem is attached.

Example:

```text
/data
```

### What is Mounting?
Mounting is the process of making a storage device (HDD, SSD, USB, CD/DVD, etc.) accessible by attaching it to a directory (mount point) in the Linux file system.

Example:

```bash
mount /dev/sdb1 /data
```

### Easy Way to Remember

```text
/data
   ↓
Mount Point


mount /dev/sdb1 /data
   ↓
Mounting Operation
```

**In short:**

* **Mount Point** → Where the filesystem is attached.
* **Mounting** → The process of attaching the filesystem.


## Why is Mounting Required?
Linux cannot access a storage device directly.

Before using a partition or disk, it must be mounted to a directory.

## How Mounting Works

Example:

Suppose a new disk is attached.

```text
New Disk
    ↓
Partition Created
    ↓
Filesystem Created (ext4/XFS)
    ↓
Mounted to /data
    ↓
Accessible as /data
```

Now users can store files in:

```text
/ data
```

`/data` becomes the entry point to access the files stored on `/dev/sdb1`.

## Types of Mounting

### 1. Temporary Mount

- Exists only until the next reboot.
- Must be mounted manually.

Example:

```bash
mount /dev/sdb1 /mnt
```

After reboot:
```text
Mount is removed.
```

### 2. Permanent Mount

- Automatically mounts during system boot.

Configured in:

```text
/etc/fstab
```
## Mounting Process (Interview Flow)

Always remember this sequence.
```text
Attach Disk
       ↓
Detect Disk
       ↓
Create Partition
       ↓
Create Filesystem
       ↓
Create Mount Point
       ↓
Mount Filesystem
       ↓
Verify Mount
       ↓
Add entry in /etc/fstab (Permanent)
```
Interviewers love asking this.

## Unmounting
Unmounting is the process of safely detaching a mounted filesystem from the Linux directory tree.

## /etc/fstab
/etc/fstab (File System Table) is a configuration file that stores information about filesystems that should be mounted automatically during system boot.

## UUID (Universally Unique Identifier)
A UUID is a unique identifier assigned to a filesystem.
It is preferred over device names (/dev/sdb1) because UUID remains the same even after reboot, while device names may change

 # Commands (in sequence)
#### Step 1 –Check Available Disks
##### #lsblk 

Purpose: Displays disks, Partitions, Mount points

#### Step 2 –Check Filesystem & UUID
##### #blkid

Purpose: Shows UUID, Filesystem type, Partition information

#### Step 3 –Create Mount Point
##### #mkdir /data

Purpose: Creates the directory where the filesystem will be mounted.

#### Step 4 –Mount the Filesystem
##### #mount /dev/sdb1 /data

Purpose: Attaches the filesystem to /data.

#### Step 5 –Verify Mount
##### #df -h or #mount

Purpose: Confirms the filesystem is mounted successfully.

#### Step 6 – Configure Permanent Mount
##### #vi /etc/fstab

#### Step 7 –Test /etc/fstab
##### #mount -a

Purpose: Mounts all filesystems listed in /etc/fstab.
         Checks for configuration errors without rebooting.

Interview Tip: Always run mount -a after editing /etc/fstab. It helps detect mistakes before restarting the system.

#### Step 8 – Unmount Filesystem

##### #umount /data  or  #umount /dev/sdb1
---

# 3.5) Linux File Links

Linux has two types of links:

1. **Hard Link**
2. **Soft Link (Symbolic Link)**

-

##  Hard Links

A **hard link** is another filename that points to the **same inode** as the original file.

```text
file1 ─────┐
           ↓
        inode
           ↓
          data
           ↑
file2 ─────┘
````

Both filenames access the **same file data**.

### Create a Hard Link

```bash
touch file1
ln file1 file2
```

Check inode numbers:

```bash
ls -li
```

Example:

```text
12345 -rw-r--r-- 2 user user 0 file1
12345 -rw-r--r-- 2 user user 0 file2
```

Both files have the **same inode number**.

The `2` represents the **link count**.

### If the Original File Is Deleted

```bash
rm file1
```

`file2` will still work because it points to the same inode.

```text
file1 ──X
          \
           inode → data
          /
file2 ──┘
```

The data is removed only when the **last hard link** is deleted.

### Hard Link Limitations

* Cannot cross **filesystems/partitions**.
* Cannot normally be created for **directories** by regular users.
* Both links must be on the **same filesystem**.

### Find Hard Links

Find the inode:

```bash
ls -i file1
```

Then search for the same inode:

```bash
find / -inum <inode_number>
```

---

##  Soft Links

A **soft link**, also called a **symbolic link (symlink)**, is a special file that stores the **path/reference** to another file.

```text
file2 → file1 → inode → data
```

The soft link has its **own inode**.

### Create a Soft Link

```bash
ln -s file1 file2
```

Check it:

```bash
ls -l
```

Output:

```text
file2 -> file1
```

### Dangling / Broken Link

A **dangling link** is a soft link whose target no longer exists.

For example:

```text
file2 → file1 → inode → data
```

If we delete the target:

```bash
rm file1
```

The link remains:

```text
file2 → file1
           X
```

Now `file2` is a **dangling/broken symbolic link** because its target `file1` does not exist.

A dangling link can also occur if the target file is **moved or renamed**.

### Soft Link Limitations

* It can become broken if the target is deleted, moved, or renamed.
* It does not point directly to the target's inode.

### Advantages of Soft Links

* Can cross **different filesystems**.
* Can point to **directories**.
* Useful when you want a convenient path/alias to another file or directory.

---

### Hard Link vs Soft Link

| Feature              | Hard Link        | Soft Link                             |
| -------------------- | ---------------- | ------------------------------------- |
| Command              | `ln file1 file2` | `ln -s file1 file2`                   |
| Inode                | Same inode       | Different inode                       |
| Points to            | Inode            | Path/name                             |
| Cross filesystem     | ❌ No             | ✅ Yes                                 |
| Directory link       | ❌ Generally no   | ✅ Yes                                 |
| Target deleted       | Link still works | Becomes dangling/broken               |
| Target moved/renamed | Still works      | May become dangling                   |
| Link count           | Increases        | Does not increase target's link count |

---

### Commands to Remember

```bash
# Create hard link
ln source destination

# Create soft link
ln -s source destination

# Check inode numbers
ls -li

# Find files having the same inode
find / -inum <inode_number>
```

> **Interview One-Liner:**
> A **hard link** points to the same inode as the original file, while a **soft link** points to the pathname of the original file.

---

 # Module 8: Storage Management
 
 ## 8.1.1) Disk vs Partition
 
 ### Partition

- It is a method by which we can break a disk into small parts.
- These partitions can be used to store OS files and personal data files.

### Types

1. Standard partition
2. Swap
3. RAID
4. LVM

### Types of Data

1. OS defined files or directory or folders.
2. Custom data
   - Created by any user for application backup or software data or sharing data.

**OS defined data** => During the OS installation + after installing any application.

**Custom Data** => After the OS installation only.

---

### In Windows

### Disk

Partition => C:\ , D:\ , E:\ , F:\

- C:\ => use to store os file & we can also use it for the data.
- D:\ E:\ F:\ => recommended drive to store data.

---

### In Linux

- There is no concept of C:\ D:\ E:\ F:\ drive.
- Whole OS will be represented by `/`

`/` => Root filesystem or root partition or parent directory.

---

# /

- It contains various standard Linux directories.
- We can also create new directory over the OS partition to store any personal data.

### Types of Data

1. Default data
2. Custom data

```bash
#
```

=> default data

```bash
mkdir /data
mkdir /movie
```

=> custom data

Disk => 20 GB

15 GB => /

=> OS directories + default data

=> Remaining space for OS files + custom data.

---

### Disk Name

#### 1) PATA or IDE

```text
hda
```

```text
hda1  hda2  hda3
```

```text
hdb
```

```text
hdb1  hdb2
```

#### 2) SATA

```text
sda
```

```text
sda1  sda2  sda3 .... etc
```

#### 3) Virtual Disk

```text
vda
```

```text
vda1  vda2
```

---

### Disk partition during OS installation

#### 1) Automatic

30 GB => machine will define the size automatically.

#### 2) Custom

We can define the space as per demand.

---

### Types of partition creation Technology

#### 1) Standard method

- Fixed size partition.

#### 2) LVM method

- Logical Volume Manager => recommended.
- We can extend or reduce this logical volume size in future.

---

Partition creation commands:-
1) Fdisk ==>
    - supports DOS mode only
   - runs perfectly on rhel 5,6,7 servers
      
           
3) Parted ==> 
   - supports DOS + gpt
   - recommended for rhel 8 servers
   - due to gpt, mbr limit increases here.   
   
            

---
 
## 8.1.2) Primary / Extended / Logical Partitions

### Primary / Extended / Logical Partitions

These concepts are primarily associated with **MBR**.

--

### Primary Partition

Traditional MBR allows up to:

```text
4 primary partitions
````

Example:

```text
/dev/sda1
/dev/sda2
/dev/sda3
/dev/sda4
```

--

### Extended Partition

An extended partition acts as a **container for logical partitions**.

Example:

```text
/dev/sda
   |
   +-- /dev/sda1 → Primary
   |
   +-- /dev/sda2 → Primary
   |
   +-- /dev/sda3 → Extended
          |
          +-- /dev/sda5 → Logical
          |
          +-- /dev/sda6 → Logical
          |
          +-- /dev/sda7 → Logical
```
```
```
---

## 8.1.3) MBR vs GPT

### First understand the basic idea

Suppose you have a **1 TB hard disk**.

You may want to divide it into partitions:

```text
1 TB Disk
   |
   +-- /dev/sda1  →  200 GB
   |
   +-- /dev/sda2  →  300 GB
   |
   +-- /dev/sda3  →  500 GB
````

**MBR and GPT decide how these partitions are recorded and managed on the disk.**

--

## MBR

**MBR = Master Boot Record**

It is the **older partitioning method**.

The important limitations are:

```text
MBR
 |
 +-- Maximum 4 Primary Partitions
 |
 +-- Around 2 TB traditional disk-size limit
```

For example:

```text
/dev/sda
   |
   +-- /dev/sda1   Primary
   +-- /dev/sda2   Primary
   +-- /dev/sda3   Primary
   +-- /dev/sda4   Primary
```

If you need more partitions, MBR uses **Extended + Logical partitions**, which makes things more complicated.

--

## GPT

**GPT = GUID Partition Table**

GPT is the **modern partitioning scheme** used to organize partitions on a disk.
It removes most of the limitations of MBR:

### Advantages of GPT

- Supports **very large disks**
- Supports **many partitions**
- Has **more robust partition metadata**
- Commonly used with **UEFI systems**
- Modern Linux systems commonly use **GPT**

### Example

```text
/dev/sda
   |
   +-- /dev/sda1
   +-- /dev/sda2
   +-- /dev/sda3
   +-- /dev/sda4
   +-- /dev/sda5
   +-- ...
````
---



