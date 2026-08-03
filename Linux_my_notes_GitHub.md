# Linux Administration & Engineering Roadmap

## Module 1: Linux Fundamentals

### Topics
- Linux Introduction
- Linux Architecture
- Linux Installation
- Shell Basics

---

## Module 2: Linux Boot Process

### What is the Linux Boot Process?

The Linux boot process is the sequence of steps that a Linux system follows from the moment it is powered on until the user reaches the login prompt or desktop environment.

### Stages of the Linux Boot Process

1. BIOS / UEFI
2. MBR / GPT
3. GRUB Bootloader
4. Kernel Initialization
5. systemd (Init System)
6. Login Prompt / Desktop

---

### 1. BIOS / UEFI (Power-On Self-Test)

**Think of BIOS/UEFI as the Security Guard.**

When the system powers on:

- BIOS (Basic Input Output System) or UEFI performs POST (Power-On Self-Test)
- Checks hardware components:
  - RAM
  - CPU
  - Keyboard
  - Storage Devices
- Searches for a bootable device (HDD, SSD, USB)
- Loads the MBR or EFI partition into memory

---

### 2. MBR / GPT

#### MBR (Master Boot Record)

Contains:

- Bootloader information
- Partition table

Location:

- First **512 bytes** of the boot disk

Responsibilities:

- Loads the first stage of the bootloader (GRUB)
- Does **not** boot Linux directly

> BIOS reads the MBR and executes its boot code. The MBR's sole purpose is to locate and start the bootloader.

---

### 3. GRUB (Grand Unified Bootloader)

**Think of GRUB as the Manager.**

Responsibilities:

- Displays boot menu
- Loads Linux Kernel (`vmlinuz-<version>`)
- Loads `initramfs`

Configuration Files:

**RHEL/CentOS**
```bash
/boot/grub2/grub.cfg
```

**Ubuntu/Debian**
```bash
/boot/grub/grub.cfg
```

Example Boot Menu:

- Ubuntu
- Windows
- Recovery Mode

GRUB loads:

- Linux Kernel
- initramfs

into RAM.

---

### 4. Kernel Initialization

**Think of the Kernel as the CEO.**

Once loaded:

- Initializes CPU and memory
- Loads device drivers
- Detects hardware
- Mounts the root filesystem
- Starts PID 1 (`systemd`)

#### What is initramfs?

`initramfs` (Initial RAM Filesystem) is a temporary filesystem loaded into memory.

Purpose:

- Contains required drivers
- Helps locate and mount the real root filesystem

After the root filesystem is mounted:

- initramfs is discarded

---

### 5. systemd (PID 1)

Modern Linux distributions use **systemd** as the first userspace process.

Responsibilities:

- Starts services (daemons)
- Mounts filesystems
- Controls boot targets
- Manages background services

Examples of Services:

- Network
- SSH
- Bluetooth
- Printer
- Audio
- GUI Display Manager

---

### 6. Login Screen

After services start:

- Login prompt appears
- User enters username and password
- Shell starts

Examples:

```text
Username:
Password:
```

Common Shells:

- bash
- zsh
- sh

---

## Complete Linux Boot Flow

```text
Power Button
      ↓
BIOS / UEFI
      ↓
POST
      ↓
Read MBR / EFI
      ↓
GRUB Bootloader
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

---

## Boot Troubleshooting Commands

### Display Kernel Messages

```bash
dmesg | less
```

Why use `less`?

Because `dmesg` can generate hundreds of lines, making output easier to read page-by-page.

---

### Last Boot Time

```bash
who -b
```

---

### System Boot Duration

```bash
systemd-analyze
```

---

### Services Taking Longest During Boot

```bash
systemd-analyze blame
```

---

## Linux Boot Process Summary

### BIOS / UEFI

- Performs POST
- Checks hardware
- Loads bootloader

### GRUB

- Loads kernel
- Provides OS selection menu

### Kernel

- Initializes hardware
- Loads drivers
- Mounts filesystem
- Starts systemd

### systemd

- Starts services
- Mounts additional filesystems
- Reaches target state

### Targets

Common Targets:

- `multi-user.target`
- `graphical.target`

### Login

- Displays login prompt
- Launches shell or desktop

---

## Module 3: Linux File System

### What is a File System?

A file system is the method Linux uses to:

- Store data
- Organize data
- Retrieve data

Think of it as a library:

| Component | Library Example |
|------------|----------------|
| Hard Disk | Library Building |
| Directory | Shelf |
| File | Book |
| File System | Catalog |

---

## Filesystem Hierarchy Standard (FHS)

FHS defines where files and directories should be stored in Linux.

Examples:

| Purpose | Location |
|----------|------------|
| Configuration Files | `/etc` |
| User Directories | `/home` |
| Logs | `/var/log` |
| Boot Files | `/boot` |

---

## Linux Directory Structure

Everything starts from the Root Directory:

```text
/
├── boot
├── bin
├── sbin
├── lib
├── etc
├── usr
├── home
├── root
├── tmp
├── mnt
├── media
├── dev
├── proc
├── sys
├── run
├── var
├── opt
└── srv
```

---

### `/boot`

Contains boot-related files:

- Linux Kernel
- initramfs
- GRUB files

Examples:

```text
vmlinuz
initramfs
grub.cfg
```

---

### `/bin`

Contains essential user commands.

Examples:

```bash
ls
cp
mv
cat
pwd
mkdir
rm
echo
```

---

### `/sbin`

Administrative commands.

Examples:

```bash
fdisk
shutdown
reboot
mkfs
fsck
ip
```

---

### `/lib`

Contains shared libraries required by:

- Programs
- Kernel

Directories:

```text
/lib
/lib64
/usr/lib
/usr/lib64
```

---

### `/etc`

System-wide configuration files.

Examples:

```text
/etc/passwd
/etc/shadow
/etc/fstab
/etc/hosts
/etc/resolv.conf
```

---

### `/usr`

Contains applications and resources.

Examples:

```text
/usr/bin
/usr/sbin
/usr/lib
/usr/share
```

---

### `/home`

Home directories for regular users.

Example:

```text
/home/user1
```

---

### `/root`

Home directory for root user.

```text
/root
```

---

### `/tmp`

Temporary storage.

Features:

- Writable by all users
- Often cleared after reboot

---

### `/mnt`

Temporary mount location.

Example:

```bash
mount /dev/sdb1 /mnt
```

---

### `/media`

Used for removable devices.

Examples:

- USB Drives
- CD/DVD
- External Hard Drives

---

### `/dev`

Contains device files.

Examples:

```text
/dev/sda
/dev/sdb
/dev/null
```

---

### `/proc`

Virtual filesystem containing process and kernel information.

Examples:

```bash
cat /proc/cpuinfo
cat /proc/meminfo
```

---

### `/sys`

Provides kernel and hardware information.

---

### `/run`

Stores runtime information.

Examples:

- PID files
- Sockets
- Runtime data

---

### `/var`

Stores variable data.

Examples:

```text
Logs
Cache
Mail
Databases
```

Important Directory:

```text
/var/log
```

---

### `/opt`

Optional third-party software.

Example:

```text
/opt/google
```

---

### `/srv`

Stores data served by services.

Examples:

- FTP content
- Web server files
- Application data

---

## File Types in Linux

Linux supports seven file types.

### 1. Regular File (-)

Stores user data.

Example:

```text
photo.jpg
resume.pdf
```

Output:

```bash
-rw-r--r--
```

---

### 2. Directory (d)

Stores references to files and directories.

Create Directory:

```bash
mkdir Linux
```

Output:

```bash
drwxr-xr-x
```

---

### 3. Symbolic Link (l)

Similar to a Windows shortcut.

Create:

```bash
ln -s file.txt shortcut
```

Output:

```bash
lrwxrwxrwx
```

---

### 4. Character Device (c)

Transfers data character-by-character.

Example:

```text
/dev/tty
/dev/null
```

Output:

```bash
crw-rw-rw-
```

---

### 5. Block Device (b)

Transfers data in blocks.

Examples:

```text
/dev/sda
/dev/sdb
```

Output:

```bash
brw-rw----
```

---

### 6. Named Pipe (p)

Used for process communication.

Create:

```bash
mkfifo mypipe
```

Output:

```bash
prw-r--r--
```

---

### 7. Socket (s)

Used for communication between applications.

Find socket files:

```bash
find /run -type s
```

---

## Identifying File Types

### Method 1

```bash
ls -l
```

### Method 2

```bash
file filename
```

Examples:

```text
-rw-r--r--  file.txt
drwxr-xr-x  folder
lrwxrwxrwx  shortcut
```

The first character identifies the file type.

---

## Inodes

### What is an Inode?

An inode (Index Node) is a data structure that stores metadata about a file.

Think of it as a file's identity card.

### Inode Stores

- File Type
- Permissions
- Owner UID
- Group GID
- File Size
- atime
- mtime
- ctime
- Hard Link Count
- Data Block Locations

### Inode Does Not Store

- File Name
- Actual File Data

---

### View Inode Number

```bash
ls -i
```

---

### Detailed Inode Information

```bash
stat filename
```

Displays:

- Inode Number
- Permissions
- Owner
- Size
- Timestamps
- Link Count

---

## How Linux Opens a File

```text
Directory
    ↓
Find Filename
    ↓
Get Inode Number
    ↓
Read Inode
    ↓
Locate Data Blocks
    ↓
Read File Contents
```

Directories map filenames to inode numbers.

---

## Inode Behavior During Operations

### Copy (cp)

- New inode created
- New data blocks created

### Rename (mv)

Within the same filesystem:

- Filename changes
- Inode remains the same

### Delete (rm)

- Directory entry removed
- Link count decreases
- Inode deleted when no references remain

---

## Check Inode Usage

```bash
df -i
```

Useful when:

- Disk space is available
- New files cannot be created

Reason:

- Inodes are exhausted

---

## Mounting Filesystems

### What is Mounting?

Mounting is the process of attaching a filesystem to a directory so that users can access its contents.

Example:

```bash
mount /dev/sdb1 /mnt
```

Verify Mounted Filesystems:

```bash
df -h
```

or

```bash
mount
```

Common Mount Points:

- `/mnt`
- `/media`

Persistent Mounting Configuration:

```text
/etc/fstab
```

View Current Block Devices:

```bash
lsblk
```

---

## Remaining Modules

### Module 4: Linux Commands

- Basic Commands
- File Management
- Text Processing
- Searching
- Compression
- Redirection & Pipes

### Module 5: User Administration

- Users & Groups
- Password Management
- Permissions
- ACL
- Hard & Soft Links

### Module 6: Process & Package Management

- Process Management
- Job Control
- Signals
- RPM / YUM / DNF / APT

### Module 7: Service Management

- systemd
- Services
- Targets
- Journald

### Module 8: Storage Management

- Partitions
- Filesystems
- Mounting
- LVM
- Swap
- RAID
- Disk Quotas

### Module 9: Networking

- Networking Basics
- IP Configuration
- DNS
- SSH
- Firewall
- Network Troubleshooting

### Module 10: Server Administration

- Logs
- NFS
- Samba
- Apache
- NGINX
- FTP

### Module 11: Automation

- Cron Jobs
- At Jobs
- Bash Shell Scripting

### Module 12: Security

- SELinux
- AppArmor
- sudo & Password Policies
- SSH Hardening

### Module 13: Monitoring & Performance

- Log Analysis
- System Monitoring
- Performance Monitoring
- Resource Management

### Module 14: Virtualization & Containers

- VMware / KVM
- Docker
- Podman
- Cloud Linux (AWS)

### Module 15: Linux Troubleshooting

- Boot Issues
- Disk & Filesystem Issues
- Network Issues
- Service Failures
- Performance Issues
- Permission Issues

### Module 16: Interview Preparation

- Linux Interview Questions
- Real-Time Scenarios
- Command Practice
- Bash Scripting Practice

---
**Linux Administration Training Notes (README.md)**
