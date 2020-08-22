### (AMD)Post install:
    $ sudo pacman -Syu --needed amd-ucode mesa xf86-video-amdgpu vulkan-radeon lib32-vulkan-radeon libva-mesa-driver lib32-libva-mesa-driver mesa-vdpau lib32-mesa-vdpau libva libva-vdpau-driver libvdpau-va-gl
### (Intel)Post install:
    $ sudo pacman -Syu intel-ucode mesa vulkan-intel libva-intel-driver intel-media-driver libvdpau-va-gl libva-vdpau-driver

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

