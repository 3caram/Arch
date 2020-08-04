### (AMDGPU)Post install misc soft:

    $ sudo pacman -Syu && sudo pacman -S --needed mc mesa lib32-mesa xf86-video-amdgpu vulkan-radeon lib32-vulkan-radeon libva-mesa-driver lib32-libva-mesa-driver mesa-vdpau lib32-mesa-vdpau libva libva-vdpau-driver libvdpau-va-gl pepper-flash chromium aspell cabextract cdparanoia cdrkit cvs p7zip samba unace unarj unrar zip curl wget go filezilla converseen avidemux-qt libreoffice-fresh doublecmd-gtk2

### Post install Plasma soft install::

    $ sudo pacman -Syu && sudo pacman -S --needed --noconfirm mc aspell cabextract cdparanoia cdrkit cvs p7zip python-boto python-pytz samba unace unarj unrar zip curl wget go kate okular ark dolphin dolphin-plugins kompare kdegraphics-thumbnailers kimageformats qt5-imageformats kdesdk-thumbnailers ffmpegthumbs raw-thumbnailer taglib falkon filezilla avidemux-qt libreoffice-fresh doublecmd-qt5
    
### Post install ZSH:
    $ sudo pacman -S  zsh zsh zsh-completions zsh-syntax-highlighting zsh-autosuggestions grml-zsh-config zsh-syntax-highlighting zsh-autosuggestions

### Changing to zsh shell:
# To list all installed shells, run:
    $ chsh -l
# And to set one as default for your user do:
    $ chsh -s $(which zsh)
    
### Install Yay:
    $ git clone https://aur.archlinux.org/yay.git && cd yay && makepkg -si
### Install Pamac:    
    $ yay -S pamac-aur

