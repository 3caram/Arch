## Install ARCH Linux with encrypted file-system and UEFI
The official installation guide (https://wiki.archlinux.org/index.php/Installation_Guide) contains a more verbose description.

## Download the Arch ISO
* Image from https://www.archlinux.org/

### Copy to a USB drive
Rufus for Windows or Etcher in linux are good options.

### Boot from USB drive
If the usb fails to boot, make sure that secure boot is disabled in the BIOS configuration.

### The default keyboard layout in the live session is US. To list all the supported keyboard layout:
ls /usr/share/kbd/keymaps/i386/qwerty/*.map.gz

# And then change the layout to the an appropriate one using loadkeys command. For US alt,int:
	$ loadkeys us-acentos

# More at: https://wiki.archlinux.org/index.php/Linux_console/Keyboard_configuration#Loadkeys

### Check if you have UEFI mode enabled--> More at: https://wiki.archlinux.org/index.php/Unified_Extensible_Firmware_Interface
	$ ls /sys/firmware/efi/efivars

# If this directory exists, you have a UEFI enabled system.

# More at: https://wiki.archlinux.org/index.php/Unified_Extensible_Firmware_Interface

### If have another pc on the same network, is easier to install whith ssh terminal, having the ability to copy and paste:
### Skip this to type directly the commands on target machine.
### Permit root login
### Check if “permit root login” is present and uncommented in “/etc/ssh/sshd_config”
	$ nano /etc/ssh/sshd_config
	
# Start the ssh seerver:
	$ systemctl start sshd
	
# set the password for the root live session:
  	$ passwd
	
# Determine target machine ip:
	$ ifconfig	
	
# Log in as root on target machine from the other pc:
	$ ssh root@ip_address_of_target_machine

## Select an appropriate mirror:
# Sync the pacman repository, install reflector(https://wiki.archlinux.org/index.php/Reflector).
# Get the good mirror list with reflector and save it to mirrorlist. US is used.		
	$ pacman -Syy --noconfirm reflector && reflector -c "US" -f 20 -l 16 -n 20 --sort rate --save /etc/pacman.d/mirrorlist
	
### Partition the disks:
# More info on partitioning formats and schemes: https://wiki.archlinux.org/index.php/Partitioning#Partition_scheme

# List all the disk and partitions on your system, will tell you if is /dev/sda or something else:
	$ fdisk -l

# To delete all partitions(Carefull, skip if needed data present in drive:	
	$ wipefs --all /dev/sda

# Create partitions
	$ cfdisk /dev/sda
		
	1 150MB EFI partition # EFI type
	2 250MB Boot partiton # Linux type
	2 100% size partiton # LVM type (to be encrypted).

### Create EFI & boot partitions
	$ mkfs.vfat -F32 /dev/sda1 && mkfs.ext4 /dev/sda2

### Create encrypted partitions
# More info and to try different options: https://wiki.archlinux.org/index.php/Dm-crypt
	$ cryptsetup luksFormat /dev/sda3 && cryptsetup luksOpen /dev/sda3 luks

# This creates a partion for root and a partion /home:
	$ pvcreate --dataalignment 1m /dev/mapper/luks && vgcreate vol0 /dev/mapper/luks
	$ lvcreate -L 60GB vol0 -n vol_root && lvcreate -l 100%FREE vol0 -n vol_home

### Create filesystems on encrypted partitions  
	$ mkfs.ext4 /dev/vol0/vol_root && mkfs.ext4 /dev/vol0/vol_home
		
## Mount the new system
	$ mount /dev/vol0/vol_root /mnt && mkdir /mnt/boot && mount /dev/sda2 /mnt/boot
	$ mkdir /mnt/home && mount /dev/vol0/vol_home /mnt/home && mkdir /mnt/etc
	
## Generate fstab	
	$ genfstab -U -p /mnt >> /mnt/etc/fstab
	
## Install the system 
# It installs linux-zen(https://wiki.archlinux.org/index.php/Kernel):
# It installs systemd-swap(https://github.com/Nefelim4ag/systemd-swap/blob/master/README.md#about-configuration):
# It installs zsh, (https://wiki.archlinux.org/index.php/microcode)

# No graphical interface.
	$ pacstrap /mnt zsh zsh-completions zsh-syntax-highlighting zsh-autosuggestions grml-zsh-config linux-zen linux-zen-headers linux-firmware base base-devel systemd sudo systemd-swap efibootmgr grub acpi os-prober mtools lvm2 iproute2 net-tools openssh networkmanager nano nano-syntax-highlighting reflector wget curl go git btrfs-progs dosfstools e2fsprogs exfat-utils f2fs-tools jfsutils nilfs-utils ntfs-3g reiserfsprogs udftools xfsprogs ecryptfs-utils unionfs-fuse cronie 
	
# With XFCE desktop.
	$ pacstrap /mnt amd-ucode zsh zsh-completions zsh-syntax-highlighting zsh-autosuggestions grml-zsh-config linux-zen linux-zen-headers linux-firmware base base-devel systemd sudo systemd-swap efibootmgr grub acpi os-prober mtools lvm2 iproute2 net-tools openssh networkmanager nano nano-syntax-highlighting reflector wget curl go git btrfs-progs dosfstools e2fsprogs exfat-utils f2fs-tools jfsutils nilfs-utils ntfs-3g reiserfsprogs udftools xfsprogs ecryptfs-utils unionfs-fuse cronie xorg mesa xorg-xkill xfce4 xfce4-goodies libcanberra libcanberra-pulse sound-theme-freedesktop xfce4-pulseaudio-plugin pulseaudio pulseaudio-alsa pavucontrol bluez bluez-utils blueman gvfs flashplugin geany xed xreader lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings hunspell hunspell-en_US xdg-user-dirs ttf-dejavu vlc firefox thunderbird libreoffice-fresh faenza-icon-theme gufw libgtop mc xdg-user-dirs
	
# Whit Plasma desktop.	
	$ pacstrap /mnt amd-ucode zsh zsh-completions zsh-syntax-highlighting zsh-autosuggestions grml-zsh-config linux-zen linux-zen-headers linux-firmware base base-devel systemd sudo systemd-swap efibootmgr grub acpi os-prober mtools lvm2 iproute2 net-tools openssh networkmanager nano nano-syntax-highlighting reflector wget curl go git btrfs-progs dosfstools e2fsprogs exfat-utils f2fs-tools jfsutils nilfs-utils ntfs-3g reiserfsprogs udftools xfsprogs ecryptfs-utils unionfs-fuse cronie xorg mesa plasma plasma-wayland-session sddm konsole hunspell hunspell-en_US packagekit-qt5 ttf-dejavu phonon-qt5-vlc gvfs flashplugin geany xed xreader lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings hunspell hunspell-en_US xdg-user-dirs ttf-dejavu vlc firefox thunderbird libreoffice-fresh faenza-icon-theme gufw libgtop mc xdg-user-dirs	
	
## Enter the new system
	$ arch-chroot /mnt $(which zsh)
	
## Replacing vi with nano:
# To replace vi with nano as the default text editor set the VISUAL and EDITOR environment variables:
	$ echo EDITOR=nano > /etc/environment && echo VISUAL=nano >> /etc/environment

## Setup systemd-swap, uncoment all zswap and swapfc, uncoment zram_enable=0 and set swapfc_enabled=1:
	$ nano /etc/systemd/swap.conf
	
## Configure mkinitcpio with modules needed for the initrd image
# Important: lvm2 must be installed inside the arch-chroot for mkinitcpio to find the lvm2 or mkinitcpio will output Error: Hook 'lvm2' cannot be found   
	$ nano /etc/mkinitcpio.conf

# Edit the /etc/mkinitcpio.conf
	$ nano /etc/mkinitcpio.conf
# Look for the HOOKS variable and add encrypt and lvm2 after keyboard. Like:
	HOOKS=(base udev autodetect modconf block encrypt lvm2 filesystems keyboard fsck)

## Regenerate initrd image
	$ mkinitcpio -p linux-zen
    
## Generate locale
# More at: https://wiki.archlinux.org/index.php/Locale
# Uncomment wanted locales in /etc/locale.gen and save the changes.	
	$ nano /etc/locale.gen
	
# Generate locales:
	$ locale-gen
	
# For US english ONLY:
	$ echo en_US ISO-8859-1 > /etc/locale.gen && echo en_US.UTF-8 UTF-8 >> /etc/locale.gen && locale-gen

	
# To set locale system wide( Do NOT set LC_ALL=C. It overrides all the locale vars and messes up special characters)# Pay attention to the UTF-8. Capital letters!
	$ echo LANG=en_US.UTF-8 > /etc/locale.conf && echo LC_ALL= >> /etc/locale.conf

# Time & Date:
Usage in the form: ln -sf /usr/share/zoneinfo/Zone/SubZone /etc/localtime
For US eastern:

	$ ln -sf /usr/share/zoneinfo/America/New_York /etc/localtime
	$ nano /etc/systemd/timesyncd.conf
	[Time]
	NTP=0.us.pool.ntp.org 1.us.pool.ntp.org 2.us.pool.ntp.org 3.us.pool.ntp.org
	$ timedatectl set-ntp true

# To set locale system wide( Do NOT set LC_ALL=C. It overrides all the locale vars and messes up special characters)# Pay attention to the UTF-8. Capital letters!

  $ echo LANG=en_US.UTF-8 > /etc/locale.conf && echo LC_ALL= >> /etc/locale.conf

timedatectl set-local-rtc 0
    
## Add user(https://wiki.archlinux.org/index.php/Users_and_groups):
# set the password for the root account using the passwd command(Skip if aleardy set for SSHD set up:
	$ passwd

# Add a system user, with autologin and relevant groups membership:
	$ groupadd -r autologin && useradd -m -g users -G wheel,storage,network,uucp,autologin,games,power,optical,scanner,lp,audio,video -s $(which zsh) MYUSERNAME && passwd MYUSERNAME

# Allow the system user to use sudo and run commands (temporary) as root:
	$ EDITOR=nano visudo

# Press CTRL + W and type wheel, then uncomment the following line:
	%wheel ALL=(ALL) ALL

## Set the hostname
	$ echo MYHOSTNAME > /etc/hostname && hostnamectl set-hostname MYHOSTNAME
		
### Grub:
# Edit /etc/default/grub
# Uncomment:
	GRUB_ENABLE_CRYPTODISK=y
# Set the line GRUB_CMDLINE_LINUX_DEFAULT as follow : 
	GRUB_CMDLINE_LINUX_DEFAULT="cryptdevice=/dev/sda3:vol0 acpi_backlight=vendor quiet"
# Then:
	$ mkdir /boot/EFI && mount /dev/sda1 /boot/EFI && grub-install --target=x86_64-efi --bootloader-id=grub_uefi --recheck
	$ cp /usr/share/locale/en\@quot/LC_MESSAGES/grub.mo /boot/grub/locale/en.mo && grub-mkconfig -o /boot/grub/grub.cfg
		
## No desktop:
	$ systemctl enable systemd-swap && systemctl enable NetworkManager.service && systemctl enable sshd && systemctl enable systemd-timesyncd.service

## For Plasma desktop:
	$ systemctl enable systemd-swap && systemctl enable sddm.service && systemctl enable NetworkManager.service && systemctl enable sshd && systemctl enable systemd-timesyncd.service

## For XFCE desktop:
	$ systemctl enable systemd-swap && systemctl enable lightdm.service && systemctl enable NetworkManager.service && systemctl enable sshd && systemctl enable systemd-timesyncd.service

### Exiting chroot.
	$ exit
	
### Umount everything:
	$ umount -a  

### Turn off:
	$ poweroff
