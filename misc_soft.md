### Install Yay:
$ git clone https://aur.archlinux.org/yay.git && cd yay && makepkg -si

### Display colored output:
Make sure you have the Color option in your /etc/pacman.conf

### Post install misc soft install:
$ yay -S kate dolphin dolphin-plugins kompare kdegraphics-thumbnailers kimageformats qt5-imageformats kdesdk-thumbnailers ffmpegthumbs raw-thumbnailer taglib zsh wget zsh-syntax-highlighting zsh-autosuggestions oh-my-zsh-git

### Changing your default shell
# To list all installed shells, run:
$ chsh -l
# And to set one as default for your user do:
$ chsh -s /usr/bin/zsh

