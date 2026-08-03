## Linux Directory Structure

Everything on Linux starts from a single directory called the **root directory**.

Unlike Windows, Linux doesn’t use drive letters such as C: / D:. Everything begins from `/`.

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
- Examples: Linux Kernel (vmlinuz), initramfs, GRUB files, Boot configuration

### 2.) /bin (User commands)

- Meaning: Binary executables.
- Contains essential commands required by all users.
- Examples: `ls`, `cp`, `mv`, `cat`, `pwd`, `mkdir`, `rm`, `echo`

### 3.) /sbin (Admin commands)

- Mostly used by the root user.
- Usually requires administrative privileges.
- Again, on many modern systems, `/sbin` is linked to `/usr/sbin`.
- Examples: `fdisk`, `reboot`, `shutdown`, `mkfs`, `fsck`, `ip`

### 4.) /lib (Libraries)

- Contain shared libraries needed by programs and the kernel.
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
- Examples: `/usr/bin`, `/usr/sbin`, `/usr/lib`, `/usr/share`

### 7.) /home (Normal users)

- Contains home directories of normal users.
- Example: `/home/user1`

### 8.) /root (Root user's home)

- Home directory of the **root user**.

### 9.) /tmp

- Stores temporary files.
- Usually cleared after reboot.
- Any user can write here.

### 10.) /mnt

- Temporary mount point used by administrators.
- Example:

```bash
mount /dev/sdb1 /mnt
```

### 11.) /media

- Used for automatically mounted removable devices.
- Examples: USB drive, CD/DVD, External HDD

### 12.) /dev

- Contains device files.
- In Linux, every hardware device is represented as a file.
- Examples: `/dev/sda`, `/dev/sdb`, `/dev/null`

### 13.) /proc

- A virtual filesystem.
- It doesn't store files on disk.
- Instead, it provides live information about:
  - CPU
  - Memory
  - Running processes

Example:

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
- Examples: PID files, Sockets, Runtime service information.
- Contents are usually cleared after reboot.

### 16.) /var

- Stores variable data.
- Examples:
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
- Example: `/opt/google`

### 18.) /srv

- Contains data served by services.
- Examples:
  - FTP server files
  - Web server content
  - Application data

# 1.) File Types & Inodes

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

### Seven Linux File Types

| Symbol | File Type | Description |
|----------|----------|----------|
| - | Regular File | Stores data (text, images, videos, scripts, etc.) |
| d | Directory | Contains files and folders |
| l | Symbolic (Soft) Link | Points to another file or directory |
| c | Character Device | Transfers data one character at a time |
| b | Block Device | Transfers data in blocks |
| p | Named Pipe (FIFO) | Used for communication between processes |
| s | Socket | Used for communication between applications |

### I. Regular File (-)

- Stores actual user data.
- Example: `photo.jpg`, `resume.pdf`
- Command:

```bash
ls -l
```

Output:

```text
-rw-r--r--
```

### II. Directory (d)

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

### III. Symbolic Link (l)

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

### IV. Character Device (c)

- Transfers data character by character.
- Example:

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

### V. Block Device (b)

- A block device stores data in fixed-size blocks.
- Examples:
  - HDD
  - SSD
  - USB Drive

- Linux device files:
  - `/dev/sda`
  - `/dev/sdb`

Check:

```bash
ls -l /dev/sda
```

Output:

```text
brw-rw----
```

### VI. Named Pipe (p)

- Used for communication between two processes.

Create:

```bash
mkfifo mypipe
```

Output:

```text
prw-r--r--
```

### VII. Socket (s)

- Used for communication between applications.
- Example:

```text
Web server ↔ Database
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

Important:

An inode does **not** store:

- File name
- Actual file data

It stores information about the file, while the data itself is stored in data blocks and the filename is stored in the parent directory entry.

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
- Location of Data Blocks

It does **not** store:

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

### Rename (mv)

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

# 3.) Mounting Filesystems

## What is Mounting?

Mounting is the process of making a storage device (HDD, SSD, USB, CD/DVD, etc.) accessible by attaching it to a directory (mount point) in the Linux file system.

## Why is Mounting Required?

Linux cannot access a storage device directly.

Before using a partition or disk, it must be mounted to a directory.

## How Mounting Works

Example:

```text
New Disk
→ Partition Created
→ Filesystem Created (ext4/XFS)
→ Mounted to /data
→ Accessible as /data
```

- Now users can store files in `/data`
- `/data` becomes the entry point to access the file stored on `/dev/sdb1`

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
