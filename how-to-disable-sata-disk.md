# How to Disable SATA Disk

This note describes how to get block device file name, search for disk controller device address on PCI bus and disable selected controller from the system.

## Find Disk Name and Disk Controller Device Address

Show all block devices available in the system:

    $ ls -l /sys/block

The output will look like this:

    total 0
    lrwxrwxrwx 1 root root 0 Mar 26 17:13 loop0 -> ../devices/virtual/block/loop0
    lrwxrwxrwx 1 root root 0 Mar 26 17:13 loop1 -> ../devices/virtual/block/loop1
    lrwxrwxrwx 1 root root 0 Mar 26 17:13 loop2 -> ../devices/virtual/block/loop2
    lrwxrwxrwx 1 root root 0 Mar 26 17:13 loop3 -> ../devices/virtual/block/loop3
    lrwxrwxrwx 1 root root 0 Mar 26 17:13 loop4 -> ../devices/virtual/block/loop4
    lrwxrwxrwx 1 root root 0 Mar 26 17:13 loop5 -> ../devices/virtual/block/loop5
    lrwxrwxrwx 1 root root 0 Mar 26 17:13 loop6 -> ../devices/virtual/block/loop6
    lrwxrwxrwx 1 root root 0 Mar 26 17:13 loop7 -> ../devices/virtual/block/loop7
    lrwxrwxrwx 1 root root 0 Mar 26 17:13 sda -> ../devices/pci0000:00/0000:00:1f.2/ata1/host0/target0:0:0/0:0:0:0/block/sda
    lrwxrwxrwx 1 root root 0 Mar 26 17:13 sdb -> ../devices/pci0000:00/0000:00:14.0/usb4/4-2/4-2:1.0/host6/target6:0:0/6:0:0:0/block/sdb

List all block devices available in the system:

    $ lsblk

The output will look like this:

    NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
    sda      8:0    0 465,8G  0 disk 
    ├─sda1   8:1    0   100M  0 part 
    ├─sda2   8:2    0   100G  0 part 
    ├─sda3   8:3    0     1K  0 part 
    ├─sda5   8:5    0    40G  0 part 
    ├─sda6   8:6    0    40G  0 part 
    ├─sda7   8:7    0    40G  0 part 
    └─sda8   8:8    0   240G  0 part 
    sdb      8:16   1 114,6G  0 disk 
    ├─sdb1   8:17   1   512M  0 part /boot/efi
    ├─sdb2   8:18   1    16G  0 part /
    └─sdb3   8:19   1    96G  0 part 

Found the **sda** block device, which is going to be disabled. The disk controller device is located on PCI bus at address **0000:00:1f.2**.

## Get Controller Device Info

Display information about found PCI device by using last part of the bus address, which is **1f.2**:

    $ lspci -s 1f.2

The output will look like this:

    00:1f.2 SATA controller: Intel Corporation 7 Series Chipset Family 6-port SATA Controller [AHCI mode] (rev 04)

## Disable Disk Controller from the System

Check for ability to remove selected disk controller from the system. Search for **remove** file inside device address directory:

    $ ls -l /sys/devices/pci0000\:00/0000\:00\:1f.2 | grep remove

The output will look like this:

    --w--w---- 1 root root 4096 Mar 26 17:13 remove

If file is available then use it to remove selected device from the system:

    $ sudo bash -c "echo '1' > /sys/devices/pci0000:00/0000:00:1f.2/remove"
