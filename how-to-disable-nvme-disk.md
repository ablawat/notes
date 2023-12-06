# How to Disable NVMe Disk

This note describes how to get block device file name, search for disk controller device address on PCI bus and disable selected controller from the system.

## Find Disk Name and Disk Controller Device Address

Show all block devices available in the system:

    $ ls -l /sys/block

The output will look like this:

    total 0
    drwxr-xr-x  2 root root 0 mar 14 17:54 .
    dr-xr-xr-x 13 root root 0 mar 14 17:54 ..
    lrwxrwxrwx  1 root root 0 mar 15 20:04 loop0 -> ../devices/virtual/block/loop0
    lrwxrwxrwx  1 root root 0 mar 15 20:04 loop1 -> ../devices/virtual/block/loop1
    lrwxrwxrwx  1 root root 0 mar 15 20:04 loop2 -> ../devices/virtual/block/loop2
    lrwxrwxrwx  1 root root 0 mar 15 20:04 loop3 -> ../devices/virtual/block/loop3
    lrwxrwxrwx  1 root root 0 mar 15 20:04 loop4 -> ../devices/virtual/block/loop4
    lrwxrwxrwx  1 root root 0 mar 15 20:04 loop5 -> ../devices/virtual/block/loop5
    lrwxrwxrwx  1 root root 0 mar 15 20:04 loop6 -> ../devices/virtual/block/loop6
    lrwxrwxrwx  1 root root 0 mar 15 20:04 loop7 -> ../devices/virtual/block/loop7
    lrwxrwxrwx  1 root root 0 mar 14 17:54 nvme0n1 -> ../devices/pci0000:00/0000:00:02.3/0000:04:00.0/nvme/nvme0/nvme0n1

List all block devices available in the system:

    $ lsblk

The output will look like this:

    NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    nvme0n1     259:0    0  1,8T  0 disk 
    ├─nvme0n1p1 259:1    0  512M  0 part /boot/efi
    ├─nvme0n1p2 259:2    0  100G  0 part /mnt
    ├─nvme0n1p3 259:3    0  100G  0 part 
    ├─nvme0n1p4 259:4    0   50G  0 part /
    └─nvme0n1p5 259:5    0   50G  0 part

Search for all devices available in the system named as *nvme*:

    find /sys/devices | egrep 'nvme[0-9][0-9]?$'

The output will look like this:

    /sys/devices/pci0000:00/0000:00:02.3/0000:04:00.0/nvme/nvme0
    /sys/devices/virtual/nvme-subsystem/nvme-subsys0/nvme0

Found the **nvme0n1** block device, which is going to be disabled. The disk controller device is located on PCI bus at address **0000:04:00.0**.

## Get Controller Device Info

Display information about found PCI device by using last part of the bus address, which is **04:00.0**:

    lspci -s 04:00.0

The output will look like this:

    04:00.0 Non-Volatile memory controller: Samsung Electronics Co Ltd NVMe SSD Controller SM981/PM981/PM983

## Disable Disk Controller from the System

Check for ability to remove selected disk controller from the system. Search for **remove** file inside device address directory:

    $ ls -l /sys/devices/pci0000\:00/0000\:00\:02.3/0000\:04\:00.0 | grep remove

The output will look like this:

    --w--w---- 1 root root 4096 Mar 26 17:13 remove

If file is available then use it to remove selected device from the system:

    $ sudo bash -c "echo '1' > /sys/devices/pci0000:00/0000:04:00.0/remove"
