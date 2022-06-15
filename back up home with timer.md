/etc/systemd/system/backup.service	

	[Unit]
	Description=Back up user folder
	After=network-online.target

	[Service]
	ExecStart=/usr/lib/rsync -a -r -v --progress --modify-window=1 -c -b -i -s -m --del -vv --ignore-errors --delete --delete-excluded --exclude='*~' --exclude={".thumbnails",".cache","Trash",".gvfs"} /home/ /backup/
	Nice=19
	KillMode=process
	SendSIGKILL=no

	[Install]
	WantedBy=multi-user.target

/etc/systemd/system/backup.timer

	[Unit]
	Description=Back up user folder Timer

	[Timer]
	OnBootSec=5min
	OnUnitInactiveSec=180min
	RandomizedDelaySec=10min
	Unit=backup.service

	[Install]
	WantedBy=multi-user.target
sudo systemctl --system enable --now backup.timer
	
