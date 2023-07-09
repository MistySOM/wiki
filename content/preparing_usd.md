# Preparing uSD Card Instructions

### Partition and Format the uSD
The uSD card should be formatted with two partitions, FAT32 and EXT4. Parted provides a terminal utility to do this, alternatively `gparted` or another graphical partition editor can be used from the GUI.

1. Install the tool (example below applies to Ubuntu or Debian based systems, use the package manager of your distribution accordingly)
```
sudo apt update
sudo apt install parted
```
2. Identify the block device name for the SD Card, for example "/dev/sdc"
```
sudo fdisk -l
```
3. Create the partition table with a FAT32 and an EXT4 partition
```
sudo parted /dev/sdc --script -- mklabel gpt
sudo parted /dev/sdc --script -- mkpart primary fat32 0% 30MiB
sudo parted /dev/sdc --script -- mkpart primary ext4 30MiB 100%
```
4. Format the partitions to FAT32 and EXT4
```
sudo mkfs.fat -F 32  /dev/sdc1
sudo mkfs.ext4 -F /dev/sdc2
```
5. Confirm the partition table is set as expected
```
sudo parted /dev/sdc --script print
```
Expected output:
```
Model: SD EB1QT (sd/mmc)
Disk /dev/sdc: 32.0GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags: 

Number  Start   End     Size    File system  Name     Flags
 1      1049kB  31.5MB  30.4MB  fat32        primary  msftdata
 2      31.5MB  32.0GB  32.0GB  ext4         primary
```
