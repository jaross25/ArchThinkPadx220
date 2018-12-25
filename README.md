# Arch ThinkPad x220 Install

Introduction

I created this guide on how to install Arch Linux on a ThinkPad x220 laptop.

1. Prereq
 
You need to go into the BIOS and make sure that UEFI is turned on and that legacy will not start before EUFI
 
2. Create USB with Arch Linux install
 
3. Partition
 
parted /dev/sda mklabel gpt
parted /dev/sda mkpart ESP fat32 1MiB 513MiB set 1 boot on
parted /dev/sda mkpart primary ext4 513MiB 25GiB
parted /dev/sda mkpart primary linux-swap 25GiB 33GiB
parted /dev/sda mkpart primary ext4 33GiB 1000%

4. Format
 
mkfs.fat -F32 /dev/sda1
mkfs.ext4 /dev/sda2
mkswap /dev/sda3
swapon /dev/sda3
mkfs.ext4 /dev/sda4
 
5. Mount
 
mount /dev/sda3/mnt
mkdir -p /mnt/boot/efi
mount /dev/sda1 /mnt/boot/efi
mkdir -p /mnt/home
mount /dev/sda4 /mnt/home

6. Pacstrap
  
pacstrap -i /mnt base base-devel
  
7. Configure fstab
  
genfstab -U -p /mnt >> /mnt/etc/fstab
 
8. Chroot & Set Root Password
 
arch-chroot /mnt /bin/bash
passwd
 
8. Language &  Locale
 
echo en_US.UTF UTF-8 > locale.gen
locale-gen
echo LANG=en_US.UTF-8 > /etc/locale.conf
 
10. Time Zone
 
ln -s /usr/share/zoneinfo/America/Chicago /etc/localtime
hwclock --systohc --utc
vi /etc/hostname
   <enterhostname>
 
11. Bootloader -UEFI
 
pacman -S grub efibootmgr
 
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=
  arch_grub --recheck
 
mkdir -p /boot/efi/EFI/boot cp /boot/efi/EFI/arch_grub/grubx64.efi /boot/ef
  i/EFI/boot/bootx64.efi
  
grub-mkconfig -o /boot/grub/grub.cfg
 
12. Few last final things
  
exit
umount /mnt/home
umount /mnt
reboot
