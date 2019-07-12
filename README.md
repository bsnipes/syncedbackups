# syncedbackups

Quick config documentation for a local server to backup its local ZFS pool snapshots to an external USB drive. In addition, rsnapshot is used to backup remote servers to a local ZFS pool that is ZFS synced to the backup USB drive.

The example here uses two USB drives - bkweek1 and bkweek2. The crontab has "zfs export ..." for both that dismount the pools on Friday morning and try to remount at 6 PM that evening. It doesn't care which drive is attached or about /dev/sd* since it lets ZFS find the pool name connected for import. After import, the crontab does the backups using rsnapshot (local non-ZFS and remote servers) and sanoid for ZFS snapshotting and syncing to the USB drive connected.

One nice thing about rsnapshot is that it uses hard links so having so many backups of a filesystem only uses a percentage of the space full backups would.

NOTE - this is only really for use where you don't have a remote backup server that can do a pull. If the main host running these scripts is compromised, it wouldn't protect your backup disks if they were mounted. This holds true for any backup solution.

ZFS Pools/Datasets in use
    tank/rsnapshot - holds all rsnapshot synced data that is then synced to the USB drives
    tank/vm - vm dataset
    bkweek1/backup - week 1 external usb hard drive pool for tank/vm dataset snapshots
    bkweek1/rsnapshot - week 1 external usb hard drive pool for tank/rsnapshot snapshots
    bkweek2/backup - week 2 external usb hard drive pool for tank/vm dataset snapshots
    bkweek2/rsnapshot - week 2 external usb hard drive pool for tank/rsnapshot snapshots

Directory Structure for the Config Files

├── etc
│   ├── crontab
│   ├── rsnapshot.conf
│   └── sanoid
│       ├── sanoid.conf
│       └── sanoid.defaults.conf
└── README.md


File Descriptions
    NOTE - some of these files contain only the relevant portions with the defaults being stripped out to make the changes easier to see... just modify your configs with pieces from these
    
    sanoid.conf - sets the ZFS datasets to backup and what snapshot schedule template to use
    
    crontab - divided into two sections
        zfs - automated sync of existing datasets to the external drives (bkweek1/bkweek2)
              exports pools on USB drives Friday morning and re-imports whatever is connected Friday evening so that it can be swapped on Fridays
        rsnapshot - runs the hourly, daily, weekly and monthly syncs to the ZFS rsnapshot dataset
        
    rsnapshot.conf
        sets the dataset for the rsnapshot backups
        sets the number of snapshots to keep - in this case 24 hourly, 7 daily, 4 weekly, 12 monthly
        tell it what directories on the localhost to backup
        tell it what other servers to backup
    
