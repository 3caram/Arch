### The snapshots are RO, we must change the flag of ALL the snapshots created in the subvolume, ex:
# btrfs property set -ts /$(mount_point)/.snapshots/#/snapshot/

	btrfs property set -ts /backup/.snapshots/7/snapshot/ ro false
	btrfs property set -ts /backup/.snapshots/6/snapshot/ ro false
	...
	btrfs property set -ts /backup/.snapshots/1/snapshot/ ro false
	
