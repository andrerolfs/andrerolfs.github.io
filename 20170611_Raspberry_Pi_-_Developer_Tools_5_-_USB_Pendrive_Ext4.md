{% include_relative menu.md %}

# 20170611 Raspberry Pi - Developer Tools 5 - USB Pendrive Ext4

Developing software on my Pi means I have to take care for the lifetime of my SD card, as it is only a flash disk and will be broken when it is written too often.

To reduce this risk I reduce the amount of write accesses by having my Git clone of my repository on an USB pendrive and the Git server repository on the SD card.

When you buy a USB pendrive it is usually formatted to work for Windows. A Git clone in Linux will not work on a Windows pendrive as Git is using the Linux filesystem informations to decide if a file was changed. The result is, that every file looks changed for Git, always.

To use the USB pendrive for Git I will format it with ext4.

First I check which devices are mounted with the command <code>df</code> :

    $ df
    Filesystem     1K-blocks    Used Available Use% Mounted on
    /dev/root       30335468 2375304  26697748   9% /
    devtmpfs          493892       0    493892   0% /dev
    tmpfs             498224       0    498224   0% /dev/shm
    tmpfs             498224    6656    491568   2% /run
    tmpfs               5120       4      5116   1% /run/lock
    tmpfs             498224       0    498224   0% /sys/fs/cgroup
    /dev/mmcblk0p1     63503   20763     42741  33% /boot
    /dev/sda1       30016560   79344  29937216   1% /media/usb0

The pendrive I will now format is <code>/dev/sda1</code>.

<b>Be careful! Check you don't need the content on your pendrive anymore and better remove all other pendrives you don't need right now!!!</b>

First I have to unmount the pendrive I want to format, I do this with

    $ sudo umount /media/usb0

Now it is not mounted anymore, let's check this :

    $ df
    Filesystem     1K-blocks    Used Available Use% Mounted on
    /dev/root       30335468 2375304  26697748   9% /
    devtmpfs          493892       0    493892   0% /dev
    tmpfs             498224       0    498224   0% /dev/shm
    tmpfs             498224    6656    491568   2% /run
    tmpfs               5120       4      5116   1% /run/lock
    tmpfs             498224       0    498224   0% /sys/fs/cgroup
    /dev/mmcblk0p1     63503   20763     42741  33% /boot

Ok, this is done.

Now I check the type of the partition :

    $ sudo fdisk -l

which tells me it is

    Device     Boot Start      End  Sectors  Size Id Type
    /dev/sda1          32 60062499 60062468 28.7G  b W95 FAT32

which was expected, let's change it to <code>LINUX</code> :

    $ sudo fdisk /dev/sda

use <code>t</code> to change the type, set it to <code>83</code> and
use <code>w</code> to write the partition table.

I my case I had to reboot my Pi, but now

    $ sudo fdisk -l

tells me

    Device     Boot Start      End  Sectors  Size Id Type
    /dev/sda1          32 60062499 60062468 28.7G 83 Linux

Now I format it with ext4 and give it the label usbstickext4 :

    $ sudo mkfs.ext4 /dev/sda1 -L usbstickext4
    mke2fs 1.42.12 (29-Aug-2014)
    /dev/sda1 contains a vfat file system
    Proceed anyway? (y,n) y
    Creating filesystem with 7507808 4k blocks and 1880480 inodes
    Filesystem UUID: 5dedb9fb-4db4-4645-9bd8-845690a7009f
    Superblock backups stored on blocks:
    	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
    	4096000

    Allocating group tables: done
    Writing inode tables: done
    Creating journal (32768 blocks): done
    Writing superblocks and filesystem accounting information: done

To mount the pendrive to <code>/mnt/usbstickext4</code> I edit <code>fstab</code> with

    $ sudo nano /etc/fstab

and add at the end of it :

    /dev/sda1 /mnt/usbstickext4 auto defaults,user 0 1

and after the next reboot the pendrive is ready :

    $ df
    Filesystem     1K-blocks    Used Available Use% Mounted on
    /dev/root       30335468 2375960  26697092   9% /
    devtmpfs          493892       0    493892   0% /dev
    tmpfs             498224       0    498224   0% /dev/shm
    tmpfs             498224    6660    491564   2% /run
    tmpfs               5120       4      5116   1% /run/lock
    tmpfs             498224       0    498224   0% /sys/fs/cgroup
    /dev/mmcblk0p1     63503   20763     42741  33% /boot
    /dev/sda1       29428068   44992  27865132   1% /mnt/usbstickext4
