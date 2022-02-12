### Create save file on user directory, including AUR packages:

        paru -Qqe > pkglist.txt && paru -Qqem >> pkglist.txt
        
### Import and install from the list:

        paru -Syu - < pkglist.txt


