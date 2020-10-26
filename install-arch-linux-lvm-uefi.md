1. get media

download from https://www.archlinux.org/download/

2. link keyboard
to find layouts type  
`find /usr/share -name \*.map.gz -print`  
`loadkeys <layout>`
> When using loadkeys skip path and extension

3. check for efi  
`ls /sys/firmware/efi/efivars`

4. verify internet connectivity  
`ip a`  

to connect using ethernet  
    1. `ip link set <interface-name> up`  
    2. `sudo dhcpcd`  

to connect using wifi  
    1. `iwctl`  
    2. `device list`  
    3. `station <device> scan`  
    4. `station device get-networks`  
    5. `station device connect <ESSID>`  


5 update clock  
`timedatectl set-ntp true`

6 partition disks
    1. find disk  
    `lsblk`  
    2. partition disks  
    `sudo fdisk <disk>`  
    
    Welcome to fdisk (util-linux 2.35.2).
    Changes will remain in memory only, until you decide to write them.
    Be careful before using the write command.
    
    Device does not contain a recognized partition table.
    Created a new DOS disklabel with disk identifier 0x1100d250.
    
    Command (m for help):
Type n for a new partition  

    Partition type  
        p   primary (0 primary, 0 extended, 4 free)  
        e   extended (container for logical partitions)  
    Select (default p):   
Type enter or p for a primary partion.

    Partition number (1-4, default 1): 
Type enter or 1 for partition 1.

    First sector (2048-41943039, default 2048): 
Type enter for default

    Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-41943039, default 41943039):
Type +512M for a 512M partition

    Command (m for help)
Type t to set partition type

    Hex code (type L to list all codes):
Type ef for EFI system partition

    Command (m for help):
Type n for a new partition  

    Partition type  
        p   primary (0 primary, 0 extended, 4 free)  
        e   extended (container for logical partitions)  
    Select (default p):   
Type enter or p for a primary partion.

    Partition number (2-4, default 2): 
Type enter or 2 for partition 2. 

    First sector (1050624-41943039, default 1050624): 
Type enter for default

    Last sector, +/-sectors or +/-size{K,M,G,T,P} (1050624-41943039, default 41943039): 
Type enter for default

    Command (m for help):

Type t to set partition type

    Partition number (1,2, default 2): 
    
Type 2 or press enter for the second partition

    Hex code (type L to list all codes):
Type 8e for Linux LVM

    Changed type of partition 'Linux' to 'Linux LVM'.

    Command (m for help):
    
Type w to write changes to disk

    The partition table has been altered.
    Calling ioctl() to re-read partition table.
    Syncing disks.

Then the second disk
`sudo fdisk <disk> ` 


    Welcome to fdisk (util-linux 2.35.2).
    Changes will remain in memory only, until you decide to write them.
    Be careful before using the write command.

    Device does not contain a recognized partition table.
    Created a new DOS disklabel with disk identifier 0x8ae032ca.

    Command (m for help): n
    Partition type
        p   primary (0 primary, 0 extended, 4 free)
        e   extended (container for logical partitions)
    Select (default p): p
    Partition number (1-4, default 1): 
    First sector (2048-41943039, default 2048): 
    Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-41943039, default 41943039): 
    
    Created a new partition 1 of type 'Linux' and of size 20 GiB.
    
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list all codes): 8e
    Changed type of partition 'Linux' to 'Linux LVM'.
    
    Command (m for help): w
    The partition table has been altered.
    Calling ioctl() to re-read partition table.
    Syncing disks.
    
verify the partition table using `lsblk`

    NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
    loop0    7:0    0 529.7M  1 loop /run/archiso/sfs/airootfs
    sr0     11:0    1   647M  0 rom  /run/archiso/bootmnt
    vda    254:0    0    20G  0 disk 
    ├─vda1 254:1    0   512M  0 part 
    └─vda2 254:2    0  19.5G  0 part 
    vdb    254:16   0    20G  0 disk 
    └─vdb1 254:17   0    20G  0 part 

7. create logical volume
	1. reserve physical volume  
	`pvcreate /dev/vda2 /dev/vdb1` 
	
			Physical volume "/dev/vda2" successfully created.
			Physical volume "/dev/vdb1" successfully created.    
	2. create volume group  
	`vgcreate vg1 /dev/vda2 /dev/vdb1`
	
			Volume group "vg1" successfully created
	3. create logical volumes  
	`lvcreate -n swap -L 6G vg1`
	
			Logical volume "swap" created.
	
	`lvcreate -n root -l 100%FREE vg1`
	
			Logical volume "root" created.
8. create filesytem  
`mkfs.ext4 /dev/mapper/vg1-root`
		
		mke2fs 1.45.6 (20-Mar-2020)
		Discarding device blocks: done                            
		Creating filesystem with 8779776 4k blocks and 2195456 inodes
		Filesystem UUID: 20430a1a-21b7-49f5-8c7c-1d2e4c5d8f07
		Superblock backups stored on blocks: 
			32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
			4096000, 7962624
		
		Allocating group tables: done                            
		Writing inode tables: done                            
		Creating journal (65536 blocks): done
		Writing superblocks and filesystem accounting information: done
		
`mkswap /dev/mapper/vg1-swap`

		Setting up swapspace version 1, size = 6 GiB (6442446848 bytes)
		no label, UUID=4eea1161-7855-4acc-9c15-b953e36987c8
		
`mkfs.fat -F32 /dev/vda1`
		
		mkfs.fat 4.1 (2017-01-24)
9. mount
`mount /dev/mapper/vg1-root /mnt`
`mkdir  /mnt/efi`
`mount /dev/vda1 /mnt/efi`
`swapon /dev/mapper/vg1-swap`

10. set mirror list  
`reflector --latest 5 -p https > /etc/pacman.d/mirrorlist`  

11. update cache
`pacman -Syy`

12. install packages to chroot enviroment
`pacstrap /mnt base base-devel lvm2 linux linux-firmware vim networkmanager grub efibootmgr`

> you do not have to install vim however you probaly should install a texteditor

13. create fstab
`genfstab -U /mnt >> /mnt/etc/fstab`

14. chroot into the new os
`arch-chroot /mnt`

15. set time
> If you are unsure of which timezone you should use then `tzselect` can help.

	timedatectl set-timezone America/New_York
	hwclock --systohc

16. set locale

	1.  edit the locale config file.
		`vim /etc/locale.gen`

	1. Uncomment the locales that you plan to use for american english uncomment the following lines.

		```
		#  en_US ISO-8859-1  
		#  en_US.UTF-8 UTF-8
		```
		
	2. Generate the locale.  
		`locale-gen`
		
		Generating locales...
  		en_US.ISO-8859-1... done
  		en_US.UTF-8... done	
		Generation complete.

17. enable networking

		systemctl enable NetworkManager

		Created symlink /etc/systemd/system/multi-user.target.wants/NetworkManager.service → /usr/lib/systemd/system/NetworkManager.service.
		Created symlink /etc/systemd/system/dbus-org.freedesktop.nm-dispatcher.service → /usr/lib/systemd/system/NetworkManager-dispatcher.service.
		Created symlink /etc/systemd/system/network-online.target.wants/NetworkManager-wait-online.service → /usr/lib/systemd/system/NetworkManager-wait-online.service.


18. create initrd image
	
	1.edit mkinitcpio config file
	
	`vim /etc/mkinitcpio.conf`
	
	2. add lvm2 to the following line in between block and filesystems
	
		HOOKS=(base udev autodetect modconf block filesystems keyboard fsck)

	  so that it looks like 

		HOOKS=(base udev autodetect modconf block lvm2 filesystems keyboard fsck)
	
	3. create image
	
	`mkinitcpio -P`
	
		==> Building image from preset: /etc/mkinitcpio.d/linux.preset: 'default'
  		  -> -k /boot/vmlinuz-linux -c /etc/mkinitcpio.conf -g /boot/initramfs-linux.img
		==> Starting build: 5.8.3-arch1-1
		  -> Running build hook: [base]
		  -> Running build hook: [udev]
		  -> Running build hook: [autodetect]
		  -> Running build hook: [modconf]
		  -> Running build hook: [block]
		==> WARNING: Possibly missing firmware for module: xhci_pci
		  -> Running build hook: [lvm2]
		  -> Running build hook: [filesystems]
		  -> Running build hook: [keyboard]
		  -> Running build hook: [fsck]
		==> Generating module dependencies
		==> Creating gzip-compressed initcpio image: /boot/initramfs-linux.img
		==> Image generation successful
		==> Building image from preset: /etc/mkinitcpio.d/linux.preset: 'fallback'
		  -> -k /boot/vmlinuz-linux -c /etc/mkinitcpio.conf -g /boot/initramfs-linux-fallback.img -S autodetect
		==> Starting build: 5.8.3-arch1-1
		  -> Running build hook: [base]
		  -> Running build hook: [udev]
		  -> Running build hook: [modconf]
		  -> Running build hook: [block]
		==> WARNING: Possibly missing firmware for module: aic94xx
		==> WARNING: Possibly missing firmware for module: wd719x
		==> WARNING: Possibly missing firmware for module: xhci_pci
		  -> Running build hook: [lvm2]
		  -> Running build hook: [filesystems]
		  -> Running build hook: [keyboard]
		  -> Running build hook: [fsck]
		==> Generating module dependencies
		==> Creating gzip-compressed initcpio image: /boot/initramfs-linux-fallback.img
		==> Image generation successful
19. set root password 
`passwd`

20. install grub

	1. install grub to EFI System partition
		
		grub-install --target=x86_64-efi --efi-directory=/efi --bootloader-id=archlinux

	2. Create config file

		grub-mkconfig -o /boot/grub/grub.cfg

21. reboot system
		
		exit
		reboot
                                                                                                 57,1          Bo
