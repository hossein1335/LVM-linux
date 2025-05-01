# Guide to Adding a New Disk to LVM in Linux

This document outlines the steps to add a new disk to a **Physical Volume (PV)**, extend a **Volume Group (VG)**, and expand a **Logical Volume (LV)** in a Linux system using LVM.

## Prerequisites
- **Root** access or a user with **sudo** privileges.
- The new disk is physically connected to the system.
- LVM tools (e.g., `lvm2`) are installed.

## Steps

1. **List Available Disks**
   ```bash
   sudo lsblk
   ```
   **Description**: This command displays all disks and partitions. Identify the new disk (e.g., `/dev/sdb`).

2. **Create a New Partition on the Disk**
   ```bash
   sudo fdisk /dev/sdb
   ```
   - Press `n` to create a new partition.
   - Accept default settings for a primary partition (or configure as needed).
   - Press `w` to save changes and exit.

3. **Convert the Partition to a Physical Volume (PV)**
   ```bash
   sudo pvcreate /dev/sdb1
   ```
   **Description**: This command initializes the new partition (`/dev/sdb1`) as a **Physical Volume**.

4. **View Existing Volume Groups**
   ```bash
   sudo vgdisplay
   ```
   **Description**: This command lists existing **Volume Groups** (e.g., `ubuntu-vg`).

5. **Add the Physical Volume to a Volume Group**
   ```bash
   sudo vgextend ubuntu-vg /dev/sdb1
   ```
   **Description**: This command adds the new **Physical Volume** (`/dev/sdb1`) to the specified **Volume Group** (e.g., `ubuntu-vg`).

6. **Verify the Physical Volume Addition**
   ```bash
   sudo vgdisplay ubuntu-vg
   ```
   **Description**: This command confirms that the **Physical Volume** was successfully added to the **Volume Group**.

7. **Extend the Logical Volume with the New Space**
   ```bash
   sudo lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
   ```
   **Description**: This command allocates all available free space in the **Volume Group** to the **Logical Volume** (e.g., `ubuntu-lv`).

8. **Resize the Filesystem to Use the New Space**
   ```bash
   sudo resize2fs /dev/ubuntu-vg/ubuntu-lv
   ```
   **Description**: This command expands the filesystem (for `ext4`) to utilize the newly allocated space.

## Notes
- **Verify Names**: The names `ubuntu-vg` and `ubuntu-lv` may differ on your system. Use `vgdisplay` and `lvdisplay` to confirm the correct names.
- **Other Filesystems**: If using a filesystem other than `ext4` (e.g., `xfs`), replace `resize2fs` with the appropriate command (e.g., `xfs_growfs`).
- **Backup**: Always back up critical data before modifying disks or partitions.

## Troubleshooting
- If `pvcreate` fails, ensure the partition was created correctly.
- If `resize2fs` does not work, check the filesystem type using `df -T`.