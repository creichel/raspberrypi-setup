# External HDDs

HDDs are complex enough. Depending on the formatting of your HDD, you need some extra programs to even access them. I am using ExFat and HFS+ hard drives alike.

This guy is about automatically mount the hard drives on start to dedicated folders to easily access them

## 1. Install format helper
Depending on if you use FAT32, EXFAT, HFS, NTFS or other formats, you need such helpers to access the disk.

### Install exfat
`sudo apt-get -y install exfat-fuse exfat-utils`

### Install hfsplus
`sudo apt-get -y install hfsprogs hfsplus`

## 2. Make folder and right management

`sudo mkdir /media/{YOUR FOLDER NAME}`
`sudo chmod 777 -R /media/{YOUR FOLDER NAME}/`

## 3. Get disk information

### Get disks by number
Search for the name of the drive, something like `sda2` or `sdb2` in the list of connected drives:

`sudo fdisk -l`

You will mostly recognise it's name by its size.

### Optional: Get uuid
If you want to make sure that you always mount the right hard drives to the right folders, independent of the port you are using, you should use the unique identifier of the hard drive to always mount exactly this. For this we need the UUID.

`ls -l /dev/disk/by-uuid/`

Copy the UUID of your hard drive for the next step

## 4. Configure Raspberry Pi to always mount the hard drives in the right folder

Open the config file
`sudo nano /etc/fstab`

At the end of the file, add either

- Via port identifier: `/dev/{YOUR PORT} /media/{YOUR FOLDER NAME} hfsplus force,rw,user,noauto,x-systemd.automount 0 0`
- Via UUID: `UUID={YOUR UUID} /media/{YOUR FOLDER NAME} hfsplus force,rw,user,noauto,x-systemd.automount 0 0`
