# Module 1: Linux Fundamentals

- Linux Introduction
- Linux Architecture
- Linux Installation
- Shell Basics

# Module 2: Linux Boot Process

# Module 3: Linux File System

- FHS (Directory Structure)
- File Types & Inodes
- Mounting Filesystems

# Module 4: Linux Commands

- Basic Commands
- File Management
- Text Processing
- Searching
- Compression
- Redirection & Pipes

# Module 5: User Administration

- Users & Groups
- Password Management
- Permissions
- ACL
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
- Journald

# Module 8: Storage Management

- Partitions
- Filesystems
- Mounting
- LVM
- Swap
- RAID
- Disk Quotas

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
- tar for Backup & Restore
- rsync
- cpio
- dd (Disk/Partition Backup)
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

- # Module 2: Linux Boot Process

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
