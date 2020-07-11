### Edit configuration file, make sure is like:
	$ sudo nano /etc/X11/xorg.conf.d/20-intel.conf	
		Section "Device"
        Identifier      "Intel Graphics"
        Driver          "intel"
        Option          "AccelMethod"  "uxa"
        Option          "TearFree" "true"
        Option          "DRI" "2"
		EndSection
