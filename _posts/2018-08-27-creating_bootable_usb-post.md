---
layout: post
comments: true
title:   "Creating a bootable USB stick"
description: Create a bootable linux USB stick
date:   2018-08-27 23:30:00 +0530
categories: linux
tags: linux
published: false
redirect_from:
  - /JCU MVP/post/
  - /bootableUSB.html
---
### Creating a bootable USB stick

on mac list the disks:
```
$ diskutil list
```
Find the USB in the list
This one was: /dev/disk5

Next unmount the disk:
```
$  diskutil unmountDisk /dev/disk5
Unmount of all volumes on disk5 was successful
```
Then make disk writable

```
$ sudo fdisk -e /dev/disk5
Password:
fdisk: could not open MBR file /usr/standalone/i386/boot0: No such file or directory
Enter 'help' for information
fdisk: 1> f 1
Partition 1 marked active.
fdisk:*1> write
Writing MBR at offset 0.
fdisk: 1> exit
```
unmount the disk
```
$ diskutil unmountDisk /dev/disk5
Unmount of all volumes on disk5 was successful
```
Now install the linux mbr which can be downloaded [here](http://bit.ly/syslinux)

unzip the file and go to the mbr folder:
```
$ cd ~/Downloads/syslinux-6.03/bios/mbr/
```
Now run the disk write command:
```
$ sudo dd conv=notrunc bs=440 count=1 if=mbr.bin of=/dev/disk5
Password:
1+0 records in
1+0 records out
440 bytes transferred in 0.001509 secs (291593 bytes/sec)
```
Use the [unetbootin](https://unetbootin.github.io/) to install the ISO onto the drive.
