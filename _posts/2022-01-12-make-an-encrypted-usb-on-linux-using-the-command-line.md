---
title: "Make an encrypted USB on linux using the command line"
last_modified_at: 2022-01-12T16:20:02-05:00
categories:
  - blog
tags:
  - linux
  - luks
  - encryption
  - security
  - bash
---

This tutorial will create a 2-partition USB, with a clear 4GB available on all systems and a 2nd encrypted Linux-only partition.

**Data Deletion:** 
Following even the first few steps of this tutorial will overwrite data on the USB stick you choose. Backup BEFORE starting! Seriously do it now.
{: .notice--danger}

**File Security:**
This is for education only. Don't trust me with your secrets. Seriously, I'm just a guy on the internet. No mistakes were intentionally made, but you never know. Refer to the official [documentation](https://gitlab.com/cryptsetup/cryptsetup/). 
{: .notice--warning}

**USB usability:** The tutorial here uses Linux Unified Key Setup (LUKS) encryption which is only accessible easily on Linux. Do NOT expect cross compatibility for the enrypted partition between Windows and Linux.
{: .notice--info}

For this tutorial we will be using `fdisk` and `cryptsetup` as these are installed by default on my `Linux Mint 20.3` install.


## Setting up the partition table

Be careful with `fdisk` and `cryptsetup` commands as if you type in the wrong device (`/dev/sdX`) you can brick your system.
{: .notice--warning}

```shell
# Start a root shell
sudo su
# List all the disk info 
fdisk -l 
```

Find the information of the USB stick you intend to format. It should be of the form `/dev/sdX`.

![screenshot of fdisk -l](/assets/images/make-luks-usb/usb-info.png)

So I would have used `/dev/sdb` in place of `/dev/sdX`. Yours may vary.

Use `fdisk` to make a new partition table on the disk.
 
```shell
fdisk /dev/sdX
m : help screen
g: change partition table to GPT
# Make 1st partition of 4GB
n: add a new partition
Enter. Enter. "+4GB". Enter.
# Make a 2nd partition the rest of the USB
n: add a new partition.
Enter, Enter, Enter.
# Execute the changes.
w: Write changes.
```

Make the `/dev/sdX1` partition FAT and the `/dev/sdX2` partition EXT4.

```shell
mkfs.fat -n CLEAR /dev/sdX1
mkfs.ext4 -n ENCRYPTED /dev/sdX2
```

Your results from `lsblk` should look something like this.

![lsblk output](/assets/images/make-luks-usb/lsblk.png)

## Setting up LUKS with `cryptsetup`

It is bad form to encrypt a disk which is formatted with [all zeros](https://wiki.archlinux.org/title/Dm-crypt/Drive_preparation). So use `dd` to pipe random data into /dev/sdX2:

```shell
dd bs=4K if=/dev/urandom of=/dev/sdX2 status=progress
```

From the guide on the [archwiki](https://wiki.archlinux.org/title/Dm-crypt/Device_encryption#Encryption_options_for_LUKS_mode).
```shell
cryptsetup --type luks2 --cipher aes-xts-plain64 --hash sha256 --iter-time 2000 --key-size 256 --pbkdf argon2id --use-urandom --verify-passphrase luksFormat /dev/sdX2
```
They use the (hopefully) sensible defaults of the cryptsetup developers.


### A GUI option - GNOME disks

Using GNOME disks, open the inserted USB, Select `Your encrypted partition -> Setting Cog -> Format Partition`.

![GNOME disks menu](/assets/images/make-luks-usb/disks-menu.png)

Ensure to tick `Erase`, `Ext4` and `LUKS`.

![format LUKS](/assets/images/make-luks-usb/disks-format-LUKS.png)

And follow the prompts.

## Mounting and Unmounting

BEFORE executing commands, replace "{username}" with your username.
{: .notice--info }

### Mounting

Ensure to mount to `/media/{username}/{desired folder name}` when using Linux Mint. As this is the default USB mount location. And you want to use the default file explorer, don't you?
{: .notice--info}

```shell 
# Mount the encrypted container. You will be prompted for a password
cryptsetup luksOpen /dev/sdX1 encrypted
# Make the mount directory, if appropriate.
mkdir /media/{username}/encrypted
# Mount the file system
mount /dev/mapper/encrypted /media/{username}/encrypted.
```

{% capture notice-2 %}
If you run into any file permission issues. Use `chown -R {username}.{username} /media/{username}/encrypted`. 
Be CAREFUL as it WILL DESTROY some file METADATA.
{% endcapture %}

<div class="notice--info">
  {{ notice-2 | markdownify }}
</div>

### Unmounting

```shell
umount /media/{username}/encrypted 
# Lets clean up after ourselves
rmdir /media/{username}/encrypted
cryptsetup close /dev/mapper/encrypted
```


**Well done!**
We have now created a USB which can use its CLEAR partition on all computers (including Windows). But we have a large encrypted partition which can only be opened with your secure password on Linux.
{: .notice--success}





