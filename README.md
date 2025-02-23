# ArchLinux
Step by Step Instructions for Installing ArchLinux
1. passwd root

start SSH

2. Start SSHD

systemctl start sshd

Get IP Address

3. ip addr - get IP Address

4. Check UEFI

ls /sys/firmware/efi/efivars

5. Check Internet and Networking

ping archlinux.org -c 5

set clock

6. timedatectl set-ntp true

7. timedatectl status

8. mirror list - https://archlinux.org/mirrorlist/

9. write to /etc/pacman.d/mirrorlist

##
## Arch Linux repository mirrorlist
## Generated on 2025-02-22
##

## United Kingdom
Server = https://archlinux.uk.mirror.allworldit.com/archlinux/$repo/os/$arch
Server = https://mirror.bytemark.co.uk/archlinux/$repo/os/$arch
Server = https://repo.c48.uk/arch/$repo/os/$arch
Server = https://gb.mirrors.cicku.me/archlinux/$repo/os/$arch
Server = https://london.mirror.pkgbuild.com/$repo/os/$arch
Server = https://mirrors.gethosted.online/archlinux/$repo/os/$arch
Server = https://mirrors.melbourne.co.uk/archlinux/$repo/os/$arch
Server = https://mirror.infernocomms.net/archlinux/$repo/os/$arch
Server = https://www.mirrorservice.org/sites/ftp.archlinux.org/$repo/os/$arch
Server = https://mirror.netweaver.uk/archlinux/$repo/os/$arch
Server = https://lon.mirror.rackspace.com/archlinux/$repo/os/$arch
Server = https://mirror.server.net/archlinux/$repo/os/$arch
Server = https://repo.slithery.uk/$repo/os/$arch
Server = https://mirror.st2projects.com/archlinux/$repo/os/$arch
Server = https://mirrors.ukfast.co.uk/sites/archlinux.org/$repo/os/$arch
Server = https://mirror.cov.ukservers.com/archlinux/$repo/os/$arch
Server = https://mirror.vinehost.net/archlinux/$repo/os/$arch
Server = https://mirrors.xhosts.co.uk/arch/$repo/os/$arch

10. Create disks

Create EFI partition:

fdisk -l to find the designation for the HDD. (Most likely /dev/sda)
fdisk /dev/sda
g (to create a new partition table)
n (to create a new partition)
1
enter
+300M
t
1 (for EFI)
w
Create /root partition:

fdisk /dev/sda
n
2
enter
+30G
w
Create /home partiton:

fdisk /dev/sda
n
3
enter
enter
w

11. Format Partitions

mkfs.fat -F32 /dev/sda1
mkfs.ext4 /dev/sda2
mkfs.ext4 /dev/sda3

12. Create the /root and /home directories:

mount /dev/sda2 /mnt
mkdir /mnt/home
mount /dev/sda3 /mnt/home

13. Install Arch linux base packages:

pacstrap -i /mnt base linux linux-firmware sudo nano vim

14. Generate the /etc/fstab file:

genfstab -U -p /mnt >> /mnt/etc/fstab

15. Chroot into installed system:

arch-chroot /mnt

16. Find available time zones:

timedatectl list-timezones

ln -sf /usr/share/zoneinfo/Europe/London /etc/localtime

17, Update the Hardware clock:

hwclock --systohc

18. Set your hostname and configure hosts

echo archlinux > /etc/hostname
Update /etc/hosts with the following:
127.0.0.1	archlinux
::1		archlinux
127.0.1.1	archlinux.cyrus-consultants.co.uk	archlinux

19. set root passwd

passwd

20. Enable network

pacman -S networkmanager
systemctl enable NetworkManager

21. Other packages

pacman -S grub efibootmgr dosfstools openssh os-prober mtools linux-headers linux-lts linux-lts-headers open-vm-tools

22. Set locale:

sed -i 's/#en_US.UTF-8/en_US.UTF-8/g' /etc/locale.gen
locale-gen

23. Enable root login via SSH:

sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/g' /etc/ssh/sshd_config
systemctl enable sshd.service
passwd

24. Create EFI boot directory:

mkdir /boot/EFI
mount /dev/sda1 /boot/EFI

25. Install GRUB on EFI mode:

grub-install --target=x86_64-efi --bootloader-id=grub_uefi --recheck

26. Setup locale for GRUB:

cp /usr/share/locale/en\@quot/LC_MESSAGES/grub.mo /boot/grub/locale/en.mo

27. Write GRUB config:

grub-mkconfig -o /boot/grub/grub.cfg

28. Create swap file:

fallocate -l 2G /swapfile
chmod 600 /swapfile
mkswap /swapfile
echo '/swapfile none swap sw 0 0' | tee -a /etc/fstab

29. Create User: useradd -m -g users -G wheel adaph

passwd adaph

30. Allow user to use sudo:

EDITOR=vim visudo

#%wheel ALL=(ALL) ALL (uncomment)

31. exit
umount -a
reboot

32. reboot
