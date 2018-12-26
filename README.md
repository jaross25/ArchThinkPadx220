# Arch ThinkPad x220 Install with UEFI

## 1 Introduction

I created this guide on how to install [Arch Linux] (https://www.archlinux.org/) on a ThinkPad x220 laptop with the UEFI interface. The ThinkPad x220 I used for this install was an i5 8GB RAM 250GB SSD. I used a bootable USB drive that contained the Arch Linux 2018.12.01 release which includes Kernel version 4.19.4.

## 2 Prerequisites

Before installing, you need to make sure that USB booting is turned on as well as UEFI. You need to go into the BIOS and make sure that UEFI is turned on and that legacy will not start before EUFI.
 
## 3 Create USB with Arch Linux install

Head over to https://www.archlinux.org and download the latest ISO image of Arch Linux.

From Windows:

Download the Rufus utility (https://rufus.ie/en_IE.html) and make the USB drive bootable with the Arch ISO you just downloaded.
 
## 4 Partition

You will want to parition the HDD / SSD to turn the storage device into multiple volumes. To do this we use the `parted` utility which is a tool to manage disk partitions in linux. Below are the commands that I used:
 
`parted /dev/sda mklabel gpt`

`parted /dev/sda mkpart ESP fat32 1MiB 513MiB set 1 boot on`

`parted /dev/sda mkpart primary ext4 513MiB 25GiB`

`parted /dev/sda mkpart primary linux-swap 25GiB 33GiB`

`parted /dev/sda mkpart primary ext4 33GiB 1000%`

## 5 Format
 
`mkfs.fat -F32 /dev/sda1`

`mkfs.ext4 /dev/sda2`

`mkswap /dev/sda3`

`swapon /dev/sda3`

`mkfs.ext4 /dev/sda4`
 
## 6 Mount
 
`mount /dev/sda3/mnt`

`mkdir -p /mnt/boot/efi`

`mount /dev/sda1 /mnt/boot/efi`

`mkdir -p /mnt/home`

`mount /dev/sda4 /mnt/home`

## 7 Pacstrap
  
`pacstrap -i /mnt base base-devel`
  
## 8 Configure fstab
  
`genfstab -U -p /mnt >> /mnt/etc/fstab`
 
## 9 Chroot & Set Root Password
 
`arch-chroot /mnt /bin/bash`

`passwd`
 
## 10 Language &  Locale
 
`echo en_US.UTF UTF-8 > locale.gen`

`locale-gen`

`echo LANG=en_US.UTF-8 > /etc/locale.conf`
 
## 11 Time Zone
 
`ln -s /usr/share/zoneinfo/America/Chicago /etc/localtime`

`hwclock --systohc --utc`

`vi /etc/hostname
   <enterhostname>`
 
## 12 Bootloader -UEFI
 
`pacman -S grub efibootmgr`
 
`grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=
  arch_grub --recheck`
 
`mkdir -p /boot/efi/EFI/boot cp /boot/efi/EFI/arch_grub/grubx64.efi /boot/ef
  i/EFI/boot/bootx64.efi`
  
`grub-mkconfig -o /boot/grub/grub.cfg`
 
## 13 Few last final things
  
`exit`

`umount /mnt/home`

`umount /mnt`

`reboot`
