---
title: "Properly Auto-mount Drives in Ubuntu"
date: 2022-09-18T22:10:32-07:00
draft: false
toc: false
---


## Background

SSDs are fast. However, large ones are still quite expensive and those insane
read/write speeds are not necessary for all types of content anyway. For videos,
music, and images, it could be much easier on the wallet to store those on a
separate hard-drive, especially as HDD drives are relatively inexpensive and
large.

I personally use Linux, I prefer it over Windows for so many reasons. I tend to
use Ubuntu or Ubuntu-derivates, so this is geared towards that system
specifically. I am unsure, but I believe that these steps may be applicable to
other distros, but that is the one that I have tried this on multiple times.

## Steps

Please note that I am going to assume the folder that you wish to have the
contents of the mounted drive at will be called 'data'. Please replace in the
below instructions if you wish for the folder to have a different name.

### 1. Connect the external drive to the Ubuntu PC.
### 2. Gather drive info
1. Get the name of the drive using `sudo fdisk -l`
    1. It is likely that the drive name will start with `/dev/sd`, confirm your
       selection by double-checking the drive size.
1. Get the UUID using `sudo blkid`
    1. Using the drive name we got in the previous step, we can find the specific
       entry
### 3. Make the mount point
  1. `sudo mkdir /data`
    1. Note that there are two ways to do this: either you create the folder
       under `/media/<username>/`, or you put it elsewhere. Placing it in the
       media folder will automatically allow that user to have read and write
       access to the mount point. If that is not what is desired or you would
       like other users to also be able to access those files, consider placing
       them elsewhere.
### 4. Give users access to the mount point
  1. `sudo groupadd data`
  2. `sudo usermod -aG data <username>`
  3. `sudo chown -R :data /data`
### 5. Create new entry in `fstab`:
  1. `sudo vim /etc/fstab`
  2. Add a new line in the format: `UUID=<ID from blkid> /data    auto nosuid,nodev,nofail,x-gvfs-show 0 0`
  3. Test entry using `sudo mount -a`. If no errors, then the entry was added
     correctly and you are safe to reboot.
### 6. Reboot

<br><br>
After your computer is done rebooting, you should be able to navigate to the
location of the drive and see that it had automounted and that its contents are
available to the users that were given access.
