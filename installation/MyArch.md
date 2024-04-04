Table of Contents

- [Pre-Installation](#pre-installation)
- [Partitioning data](#partitioning-data)
- [LVM setup](#lvm-setup)
- [Filesystems](#filesystems)
- [Mount partitions](#mount-partitions)
- [Install 'arch base system'](#install-arch-base-system)
- [Configuring the system](#configuring-the-system)
- [Bootloader](#bootloader)
- [Reboot](#reboot)

#### Pre-Installation
[Arch Image](https://archlinux.org/download/)


**Create bootable media**

```sh
sudo dd if=patch-to-image.iso of=/dev/disk bs=4M`
```

**Boot from iso image**

**Connect to the internet**

*Ensure network interface is listed and enabled*

```sh
ip link
ip addr
ip -c a
```

for **Ethernet** - plug in the cable
for **Wi-Fi** - authenticate to the wireless network using `:iwctl 

```sh
[iwd] help
[iwd] device list # List all Wi-Fi devices

# If the device or its corresponding adapter is turned off, turn it on
[iwd] device *DEVICE* set-property Powered on
[iwd] adapter *ADAPTER* set-property Powered on

[iwd] station *DEVICE* scan # Initiate a scan for networks
[iwd] station *DEVICE* get-networks #List all available networks
[iwd] station *DEVICE* connect _SSID_ #Connect to a network
iwctl --passphrase _passphrase_ station _device_ connect _SSID_ #Enter passphrase

ping -c 3 archlinux.org
```

#### Partitioning data

**List disks**
`lsblk`

**Disk partitioning tools**
`cfdisk | fdisk | gdisk` 

| partition | first sector | last sector | code |
| --------- | ------------ | ----------- | ---- |
| 1         | efi          | +512M       | ef00 |
| 2         | boot         | +1G         | ef02 |
| 3         | /            | default     | 8e00 | 
`gdisk` /dev/_disk to partition_

**Clear partition table**
```sh
Command: 0
y
```

**EFI partition (linux image)**
```sh
Command: n
enter
enter
+512M
ef00
```

**Boot partition (grub-bootloader)**
```sh
Command: n
enter
enter
+4G
ef02
```

**SWAP partition (swap partition)**
```sh
Command: n
enter
enter
+____G - ()
ef00
```

**Root partition (/)**
```sh
Command: n
enter
enter
REMAINING DISK SPACE
Linux File System
```

**Write and exit**
```sh
Command: w
y
```

#### LVM setup
```sh
: pvcreate /dev/*device* //create physical volume
: pvcreate /dev/sdaX

: vgcreate *volume-group* *device* //create volume group
: vgcreate arch /dev/sdaX

: lvcreate -n *logical-volume* -l *size* *volume-group* //logical volume
: lvcreate -n root -L 20G arch
: lvcreate -n swap -L 5G arch
: lvcreate -n home -l 100%FREE arch

: lvdisplay / lvs
```

**Activate logical volumes**
```sh
: modprobe dm_mod
: vgscan
: vgchange -ay
```

#### Filesystems

**FAT32 on EFI partition (linux image)**
```sh
mkfs.fat -F32 /dev/sdaX
```

**EXT4 on Boot partition (bootloader)**
```sh
mkfs.ext4 /dev/sdaX
```

**BTRFS on root partition (root & home)**
```sh
: mkfs.btrfs -L root'&'home /dev/*volume-group*/*logical-volume* //syntax
: mkfs.btrfs -L root /dev/arch/root
: mkfs.btrfs -L home /dev/arch/home
: mkswap /dev/arch/swap
: swapon /dev/arch/swap
```

#### Mount partitions

**Mount root**
```sh
: mount /dev/sda* /mnt # if not lvm
```

```lvm mount
: mount /dev/*volume-group*/*logical-volume* /mnt
: mount /dev/arch/root /mnt
```

**Mount home**
```sh
: mount --mkdir /dev/sda* /mnt/home # if not lvm
```

```lvm mount
: mount --mkdir /dev/*volume-grou*/*logical-volume* /mnt/home
: mount --mkdir /dev/arch/home /mnt/home
```

**Mount boot**
```sh
: mount --mkdir /dev/sdaX /mnt/boot
```

**Mount efi**
```sh
: mount --mkdir /dev/sdaX /mnt/boot/efi
```

**Mount swap**
```lvm mount
: mkswap /dev/*volume-grou*/*logical-volume*
: swapon /dev/*volume-grou*/*logical-volume*
: mkswap /dev/arch/swap
: swapon /dev/arch/swap
```


#### Install 'arch base system'
```sh
: pacstrap -K /mnt base base-devel linux linux-firmware vim
```

**Load the file table**
```sh
: genfstab -U -p /mnt > /mnt/etc/fstab
```

**Chroot into installation**
```sh
: arch-chroot /mnt /bin/bash
```

#### Configuring the system

**mkinitcpio.conf**
```sh
# Add the `lvm2` hooks
# `HOOKS=(... block lvm2 filesystems fsck)
: vim /etc/mkinitcpio.conf

 # install `lvm2` for LVM support hooks
: pacman -S lvm2

# reload linux
: mkinitcpio -p linux
```

**Timezone**
```sh
ln -sf /usr/share/zoneinfo/Asia/Karachi /etc/localtime

# NTP
: vim /etc/systemd/timesyncd.conf

# Add in the NTP servers
[Time]
NTP=0.arch.pool.ntp.org 1.arch.pool.ntp.org 2.arch.pool.ntp.org 3.arch.pool.ntp.org 
FallbackNTP=0.pool.ntp.org 1.pool.ntp.org

# Enable timesyncd
: systemctl enable systemd-timesyncd.service
```

**Locale**
```sh
: vim /etc/locale.gen
: en_US.UTF-8 UTF-8` # uncomment
: locale-gen
: echo "LANG=en_US.UTF-8" > /etc/locale.conf
: echo "KEYMAP=us" > /etc/vconsole.conf
```

**Hostname**
```sh
: echo "arch" > /etc/hostname
```

**Root passwd and useradd**
```sh
: passwd # roost password

# Create user account
: useradd -m -G wheel -s /bin/fish arfan
: passwd arfan

# Add user to sudoers
: export EDITOR=vim
: visudo
: %wheel ALL=(ALL:ALL) ALL (ucomment)
```

**Network Connectivity**
```sh
: pacman -S networkmanager
: systemctl enable NetworkManager
```
#### Bootloader
```sh
# install grub and efibootmgr
: pacman -S grub efibootmgr

# Setup grub on efi partition
: grub-install --target=x86_64-efi --efi-directory=/boot/efi

# Add lvm volume to grub
: vim /etc/default/grub 

# Add in the following kernel parameters
root=/dev/mapper/arch-root

# Create Grub configuration
: grub-mkconfig -o /boot/grub/grub.cfg
: grub-mkconfig -o /boot/efi/EFI/arch/grub.cfg
```

#### Reboot
```sh
: exit
: umount -R /mnt
: reboot now
```