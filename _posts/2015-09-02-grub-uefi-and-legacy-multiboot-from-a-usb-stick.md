---
layout: post
title: "Grub UEFI and Legacy Multiboot from a USB Stick"
tags:
- Grub
---

**Caution:** Following instructions may break your computer wiping off your
harddisk. These instructions are my personal notes. They were written as a
future reference and don't mean to be a complete guide. Follow them at your own
risk.

It is convenient to have a USB stick that can boot in both UEFI and Legacy modes
and we can choose a live operating system to install from the grub menu. This
post walks through the steps to have such a USB stick. I use a 8 GB USB stick.

## Preparing USB Stick
Insert your USB flash and check its /dev/sdX

{% highlight bash %}
gokhanettin@pk5:~$ lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda      8:0    0 931.5G  0 disk 
sdb      8:16   0 238.5G  0 disk 
├─sdb1   8:17   0   250M  0 part /boot/efi
├─sdb2   8:18   0 117.2G  0 part /
└─sdb3   8:19   0   121G  0 part /home
sdc      8:32   1   7.6G  0 disk 
└─sdc1   8:33   1   7.6G  0 part 
gokhanettin@pk5:~$ 
{% endhighlight %}

Mine is `/dev/sdc`. `fdisk -l` or `dmesg | tail` also would do the trick.

Create partitions for both legacy bios boot and efi boot. Replace `/dev/sdX` with
yours.

{% highlight bash %}
sudo su
parted /dev/sdX -- mktable gpt
parted /dev/sdX -- mkpart biosgrub fat32 1MiB 4MiB
parted /dev/sdX -- mkpart efi fat32 4MiB -1
parted /dev/sdX -- set 1 bios_grub on
parted /dev/sdX -- set 2 esp on
{% endhighlight %}

Print what we have now. Here is my `/dev/sdc`.

{% highlight bash %}
root@pk5:/home/gokhanettin# parted /dev/sdc -- print
Model: Generic Flash Disk (scsi)
Disk /dev/sdc: 8179MB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags: 

Number  Start   End     Size    File system  Name      Flags
 1      1049kB  4194kB  3146kB               biosgrub  bios_grub
 2      4194kB  8178MB  8174MB               efi       boot, esp
{% endhighlight %}

Make a file system on `/dev/sdX2` labeling it MULTIBOOT. Mount it and install
legacy and uefi grub on it.

{% highlight bash %}
mkfs -t vfat -n MULTIBOOT /dev/sdX2
mount /dev/sdX2 /mnt

apt-get install grub-efi-amd64-bin grub-pc-bin grub2-common

grub-install --removable --boot-directory=/mnt \
     --target=i386-pc /dev/sdX

grub-install --removable --no-nvram --no-uefi-secure-boot \
     --efi-directory=/mnt --boot-directory=/mnt \
     --target=x86_64-efi
{% endhighlight %}

Copy your iso files into your USB stick.

{% highlight bash %}
mkdir /mnt/isos
exit # Exit from root
sudo cp ~/Downloads/*.iso /mnt/isos && sync
{% endhighlight %}

Create a `mnt/grub/grub.cfg`

{% highlight bash %}
sudo vim /mnt/grub/grub.cfg
{% endhighlight %}

Modify the following content according to your iso files and put it into your
`mnt/grub/grub.cfg`

{% highlight bash %}
set timeout=15
set default=0

# (U)EFI Graphic Protocol
insmod efi_gop
insmod efi_uga
insmod font

if loadfont ${prefix}/fonts/unicode.pf2
then
       insmod gfxterm
       set gfxmode=auto
       set gfxpayload=keep
       terminal_output gfxterm
fi

menuentry "Pardus Kurumsal5 64 Bit" {
               set isofile=isos/pardus-kurumsal5.iso
               loopback loop /$isofile
               linux (loop)/live/vmlinuz fromiso=/dev/disk/by-label/MULTIBOOT/$isofile boot=live config live-config.username=pardus live-config.user-fullname=pardus live-config.hostname=pardus live-config.timezone=Europe/Istanbul live-config.utc=yes live-config.nottyautologin live-config.locales=tr_TR.UTF-8,en_US.UTF-8 locale=tr_TR keyboard-layouts=tr keyboard-model=pc105 modprobe.blacklist=floppy file=/cdrom/install/preseed.cfg splash quiet --
               initrd (loop)/live/initrd.img
}

menuentry "Pardus ETA - 45 64 Bit" {
               set isofile=isos/pardus-eta.iso
               loopback loop /$isofile
               linux (loop)/live/vmlinuz fromiso=/dev/disk/by-label/MULTIBOOT/$isofile boot=live config live-config.username=pardus live-config.user-fullname=pardus live-config.hostname=pardus live-config.timezone=Europe/Istanbul live-config.utc=yes live-config.nottyautologin live-config.locales=tr_TR.UTF-8,en_US.UTF-8 locale=tr_TR keyboard-layouts=tr keyboard-model=pc105 modprobe.blacklist=floppy file=/cdrom/install/preseed.cfg splash quiet --
               initrd (loop)/live/initrd.img
}
{% endhighlight %}

## Make your Legacy Bootable OS boot with UEFI

Don't forget to create fat32 250MiB sdX1 partition with boot, esp flags during
installation. After installation, use a UEFI booted live system to chroot into
installed system and install uefi support.

In a live UEFI booted system, mount installed system for chroot. You can check
your installed system partition with `lsblk` or `fdisk -l` (sda1, sdb2, etc...)
replace them with sdXY in the following commands.

{% highlight bash %}
sudo su
mount /dev/sdXY /mnt # sdXY is a linux system partition
mount /dev/sdXY /mnt/boot/efi # sdXY is a EFI partition
mount -o bind /dev /mnt/dev
mount -t proc none /mnt/proc
mount -o bind /sys /mnt/sys
mount -o bind /tmp /mnt/tmp
{% endhighlight %}

chroot into installed system.

{% highlight bash %}
chroot /mnt /bin/bash
export PS1="(chroot) $PS1"
{% endhighlight %}

Get necessary packages and install UEFI support. Replace *Pardus* with your
distro name.

{% highlight bash %}
apt-get install grub-efi-amd64-bin grub2-common
grub-install --target=x86_64-efi --efi-directory=/boot/efi \
    --bootloader-id=Pardus --recheck --debug /dev/sdX
{% endhighlight %}

Generate grub configuration, exit chroot, unmount and reboot.

{% highlight bash %}
grub-mkconfig -o /boot/grub/grub.cfg
exit
umount -f /mnt/dev
umount -f /mnt/proc
umount -f /mnt/sys
umount -f /mnt/tmp
umount /mnt/boot/efi
umount /mnt
reboot
{% endhighlight %}

As a side note, you can check boot entries and change their boot order using
`efibootmgr`

{% highlight bash %}
sudo efibootmgr -v # Check boot order, you will see boot entries like Boot0003*
sudo efibootmgr -o XXXX # Computer will boot from BootXXXX* from now on.
{% endhighlight %}

## Make your UEFI Bootable OS boot with Legacy BIOS

In a live BIOS booted system, mount installed system for chroot. You can check
your installed system partition with `lsblk` or `fdisk -l` (sda1, sdb2, etc...)
replace them with sdXY in the following commands.


{% highlight bash %}
sudo su
mount /dev/sdXY /mnt # sdXY is a linux system partition
mount -o bind /dev /mnt/dev
mount -t proc none /mnt/proc
mount -o bind /sys /mnt/sys
mount -o bind /tmp /mnt/tmp
{% endhighlight %}

chroot into installed system.

{% highlight bash %}
chroot /mnt /bin/bash
export PS1="(chroot) $PS1"
{% endhighlight %}

Get necessary packages and install UEFI support. Replace *Pardus* with your
distro name.

{% highlight bash %}
apt-get install grub-pc-bin grub2-common
grub-install --boot-directory=/boot \
    --target=i386-pc /dev/sdX
{% endhighlight %}

Generate grub configuration, exit chroot, unmount and reboot.

{% highlight bash %}
grub-mkconfig -o /boot/grub/grub.cfg
exit
umount -f /mnt/dev
umount -f /mnt/proc
umount -f /mnt/sys
umount -f /mnt/tmp
umount /mnt
reboot
{% endhighlight %}

## Resources
<https://www.debian-administration.org/users/dkg/weblog/112>
