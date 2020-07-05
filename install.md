## Install ARCH Linux with encrypted file-system and UEFI

The official installation guide (https://wiki.archlinux.org/index.php/Installation_Guide) contains a more verbose description.

## Download the Arch ISO

* Image from https://www.archlinux.org/

### Copy to a USB drive
# Rufus for Windows or Etcher in linux are good options.

### Boot from USB drive

If the usb fails to boot, make sure that secure boot is disabled in the BIOS configuration.

### The default keyboard layout in the live session is US. To list all the supported keyboard layout:

ls /usr/share/kbd/keymaps/i386/qwerty/*.map.gz

# And then change the layout to the an appropriate one using loadkeys command. For US alt,int:

loadkeys us-acentos

# More at: https://wiki.archlinux.org/index.php/Linux_console/Keyboard_configuration#Loadkeys

### Check if you have UEFI mode enabled--> More at: https://wiki.archlinux.org/index.php/Unified_Extensible_Firmware_Interface

$ ls /sys/firmware/efi/efivars

# If this directory exists, you have a UEFI enabled system.

# More at: https://wiki.archlinux.org/index.php/Unified_Extensible_Firmware_Interface

## Select an appropriate mirror
# Sync the pacman repository:
		
$ pacman -Syy

# Now, install reflector(https://wiki.archlinux.org/index.php/Reflector) , to list the fresh and fast mirrors:
		
$ yes | pacman -S reflector

# Get the good mirror list with reflector and save it to mirrorlist. US is used.
# More refelector options: https://wiki.archlinux.org/index.php/Reflector

$ reflector -c "US" -f 12 -l 10 -n 12 --sort rate --save /etc/pacman.d/mirrorlist

### Partition the disks:
# More info on partitioning formats and schemes: https://wiki.archlinux.org/index.php/Partitioning#Partition_scheme

# List all the disk and partitions on your system, will tell you if is /dev/sda or something else:

$ fdisk -l

# Create partitions

$ cfdisk /dev/sda
		
 # 1 200MB EFI partition # EFI type
 # 2 300MB Boot partiton # Linux type
 # 2 100% size partiton # LVM type (to be encrypted).

### Create EFI & boot partitions

 $ mkfs.vfat -F32 /dev/sda1
 $ mkfs.ext4 /dev/sda2

### Create encrypted partitions
# More info and to try different options: https://wiki.archlinux.org/index.php/Dm-crypt
  
 $ cryptsetup luksFormat /dev/sda3
 $ cryptsetup luksOpen /dev/sda3 luks

# This creates a partion for root and a partion /home:

  $ pvcreate --dataalignment 1m /dev/mapper/luks
  $ vgcreate vol0 /dev/mapper/luks
  $ lvcreate -L 60GB vol0 -n vol_root
  $ lvcreate -l 100%FREE vol0 -n vol_home

### Create filesystems on encrypted partitions
  
  $ mkfs.ext4 /dev/vol0/vol_root
  $ mkfs.ext4 /dev/vol0/vol_home

## Mount the new system

	$ mount /dev/vol0/vol_root /mnt
	$ mkdir /mnt/boot && mount /dev/sda2 /mnt/boot
	$ mkdir /mnt/home && mount /dev/vol0/vol_home /mnt/home
	$ mkdir /mnt/etc
	
## Generate fstab

  $ genfstab -U -p /mnt >> /mnt/etc/fstab
	
## Install the system 
# It installs linux-lts(https://wiki.archlinux.org/index.php/Kernel):
# It installs systemd-swap(https://github.com/Nefelim4ag/systemd-swap/blob/master/README.md#about-configuration):
# It installs amd-ucode for amd, for intel would be intel-ucode (https://wiki.archlinux.org/index.php/microcode)
# It installs zsh, (https://wiki.archlinux.org/index.php/microcode)


$ pacstrap /mnt amd-ucode zsh zsh-completions zsh-syntax-highlighting zsh-autosuggestions grml-zsh-config linux-lts linux-lts-headers linux-firmware base base-devel systemd sudo systemd-swap efibootmgr grub acpi dosfstools os-prober mtools lvm2 iproute2 networkmanager nano nano-syntax-highlighting reflector
	
## Enter the new system
# Note: have to try to $ arch-chroot arch-chroot /mnt $(which zsh)
  
$ arch-chroot /mnt 
	
# To know what mount points are listed.

$ cat /etc/fstab
	
# To know what partitions are actually mounted.
$ cat /proc/mounts
	
## If any partition is NOT correctly listed:

$ rm /etc/fstab
	
# mount the partitions inside the chroot:
# exit chroot
# rund genfstab -U -p /mnt >> /mnt/etc/fstab again and reenter the chroot.

## Replacing vi with nano:
# To replace vi with nano as the default text editor set the VISUAL and EDITOR environment variables:
# Edit /etc/environment and add the foollowing: 

VISUAL=nano
EDITOR=nano


## Setup systemd-swap:

$ nano /etc/systemd/swap.conf

# Uncoment all zswap and swapfc, uncoment zram_enable=0

$ systemctl enable systemd-swap

## Configure mkinitcpio with modules needed for the initrd image
# Important: lvm2 must be installed inside the arch-chroot for mkinitcpio to find the lvm2 or mkinitcpio will output Error: Hook 'lvm2' cannot be found
   
$ nano /etc/mkinitcpio.conf

# Edit the /etc/mkinitcpio.conf
# Look for the HOOKS variable and add encrypt and lvm2 after keyboard. Like:

	HOOKS=(base udev autodetect modconf block encrypt lvm2 filesystems keyboard fsck)

## Regenerate initrd image

	mkinitcpio -p linux-lts
    
## Generate locale
# More at: https://wiki.archlinux.org/index.php/Locale
# Uncomment wanted locales in /etc/locale.gen
	
	nano /etc/locale.gen
	$ locale-gen
	
# To set locale system wide( Do NOT set LC_ALL=C. It overrides all the locale vars and messes up special characters)
# Pay attention to the UTF-8. Capital letters!

	$ echo LANG=en_US.UTF-8 >> /etc/locale.conf
	$ echo LC_ALL= >> /etc/locale.conf
    
## Add user(https://wiki.archlinux.org/index.php/Users_and_groups):
# set the password for the root account using the passwd command:

	$ passwd

# Add a system user:

	$ useradd -m -g users -G wheel,storage,network,uucp,games,power,optical,scanner,lp,audio,video -s $(which zsh) MYUSERNAME && passwd MYUSERNAME

# Allow the system user to use sudo and run commands (temporary) as root:

	$ EDITOR=nano visudo

# Press CTRL + W and type wheel, then uncomment the following line:

	%wheel ALL=(ALL ALL)

## Setup system clock and timezone:(https://wiki.archlinux.org/index.php/System_time)
# To list available zones:

$ timedatectl list-timezones

# To set your time zone:
# timedatectl set-timezone Zone/SubZone
# Example:

$ timedatectl set-timezone America/New_York

# To enable NTP:(https://wiki.archlinux.org/index.php/Systemd-timesyncd):

$ timedatectl set-ntp true

$ systemctl enable systemd-timedated && systemctl enable systemd-timesyncd

## Set the hostname

$ echo MYHOSTNAME > /etc/hostname
$ hostnamectl set-hostname MYHOSTNAME

## Setup network:
# Edit /etc/hosts, add:
127.0.0.1        localhost
::1              localhost
127.0.1.1        myhostname
		
### Set up Grub:
# Edit /etc/default/grub
# Uncomment GRUB_ENABLE_CRYPTODISK=y
# set as follow : GRUB_CMDLINE_LINUX_DEFAULT="cryptdevice=/dev/sda3:vol0:allow-discards quiet acpi_backlight=vendor"

	$ mkdir /boot/EFI && mount /dev/sda1 /boot/EFI
	$ grub-install --target=x86_64-efi --bootloader-id=grub_uefi --recheck
	$ mkdir /boot/grub/locale
	$ cp /usr/share/locale/en\@quot/LC_MESSAGES/gub.mo /boot/grub/locale/en.mo
	$ grub-mkconfig -o /boot/grub/grub.cfg
	
### Install Yay:
	$ cd /tmp && git clone https://aur.archlinux.org/yay.git && cd yay && makepkg -si
	
### Installing KDE Plasma desktop and some extras (Optional, skip if another desktop whanted):

	$ pacman -S mesa xorg plasma plasma-wayland-session sddm  konsole hunspell xdg-user-dirs packagekit-qt5 ttf-dejavu phonon-qt5-vlc vlc git firefox && systemctl enable sddm.service && systemctl enable NetworkManager.service

###Exiting chroot:
	$ exit
 
### Umount everything
	$ umount -a

### Turn off:
	$ poweroff
