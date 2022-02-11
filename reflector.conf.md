    $ sudo nano /etc/xdg/reflector/reflector.conf

    --save /etc/pacman.d/mirrorlist
    --protocol https
    --delay 0.25
    --latest 20
    --fastest 20
    --connection-timeout 2
    --download-timeout 2
    --sort rate

    $ sudo systemctl enable --now reflector.service
