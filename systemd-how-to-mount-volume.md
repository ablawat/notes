# How to Mount a Volume in *systemd*

This note describes how to create mount unit that is able to mount specified filesystem.

## Specify Mounted Filesystem

Get universally unique identifier and filesystem type for a volume:

    $ lsblk -f

The output will look like this:

    NAME        FSTYPE LABEL UUID                                 FSAVAIL FSUSE% MOUNTPOINT
    nvme0n1                                                                      
    ├─nvme0n1p1 vfat         D458-2805                               505M     1% /boot/efi
    ├─nvme0n1p2 btrfs        c3bc467a-e4d4-4f34-8cd1-0de95d1c6375                
    ├─nvme0n1p3 ext4         a5b302a8-86e6-4713-b6c3-103e574e8ee0                
    ├─nvme0n1p4 ext4         90984a07-1f3f-4b66-8e4d-3505e9d63888   19,1G    56% /
    └─nvme0n1p5 btrfs        e7850deb-32ca-488a-bb19-c5bcad9390ce

Found the `nvme0n1` block device, which has `nvme0n1p2` partition. The filesystem of this partition is `btrfs` and it has identifier `c3bc467a-e4d4-4f34-8cd1-0de95d1c6375`.

This partition will be a volume that is going to be mounted on `/mnt`.

## Create Mount Unit Configuration File

Create new file named after selected mount point under `/etc/systemd/system` directory:

    sudo touch /etc/systemd/system/mnt.mount

> Mount units must be named after the mount point directories they control. Example: the mount point `/home/storage` must be configured in a unit file named `home-storage.mount`.

Add content to file `/etc/systemd/system/mnt.mount`:

    [Mount]
    What=/dev/disk/by-uuid/c3bc467a-e4d4-4f34-8cd1-0de95d1c6375
    Where=/mnt
    Type=btrfs
    Options=defaults

## Apply New Configuration File

Reload all `systemd` configuration files:

    $ sudo systemctl daemon-reload

Finally, mount specified volume in the system:

    $ sudo systemctl start mnt.mount
