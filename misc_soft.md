### Post install misc soft install:

    $ sudo pacman -Syu && sudo pacman -S mc aspell cabextract cdparanoia cdrkit cvs p7zip python-boto python-pytz samba unace unarj unrar zip kate dolphin dolphin-plugins kompare kdegraphics-thumbnailers kimageformats qt5-imageformats kdesdk-thumbnailers ffmpegthumbs raw-thumbnailer taglib curl wget go

### Post install ZSH:
    $ sudo pacman -S  zsh zsh zsh-completions zsh-syntax-highlighting zsh-autosuggestions grml-zsh-config zsh-syntax-highlighting zsh-autosuggestions

### Changing to zsh shell:
# To list all installed shells, run:
    $ chsh -l
# And to set one as default for your user do:
    $ chsh -s $(which zsh)
    
### Install Yay:
    $ git clone https://aur.archlinux.org/yay.git && cd yay && makepkg -si

