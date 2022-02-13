### Create save file on user directory, including AUR packages:

        paru -Qqe > pkglist.txt && paru -Qqem >> pkglist.txt
        
### Import and install from the list:

        paru -Syu - < pkglist.txt   
        
### Remove packages not in the list:

        paru -Rsu $(comm -23 <(paru -Qq | sort) <(sort pkglist.txt))


