<img src="../files/img/2018_MistyWest_LogoCombo_FINAL_RGB.png" alt="MistyWest" width="400"/><div style="text-align: right">back to [index](../README.md)</div>

# Program eMMC flash

* To program the eMMC Flash device on MistySOM, the below instructions can be followed.
* You will need to boot with SD card first and partition eMMC Flash device. Then you can load the files from SD card to eMMC Flash.
1. Copy listed files to “/home/root” of the SD Card that you use to boot the system.

MistySOMG2L : **core-image-weston-smarc-rzg2l.tar.gz, Image** and **r9a07g044l2-smarc.dtb**

MistySOMV2L : **core-image-weston-smarc-rzv2l.tar.gz, Image** and **r9a07g054l2-smarc.dtb**

2. Boot the board with the SD card and log into Linux.

3. Partition the eMMC Flash using fdisk.

In fdisk, use the commands 'n' to make a new partition, and 'w' to write the partition (will automatically exit fdisk)
In fdisk, you can just press the Enter Key to select the default value (you do not have to type the default value)
```
root@smarc-rzg2l:~# fdisk /dev/mmcblk0
Command (m for help): n
Partition number (1-128, default 1): 1                <<<< just press ENTER for default value
First sector (34-124321758, default 2048): 2048       <<<< just press ENTER for default value
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-124321758, default 124321758): 124321758  <<<< just press ENTER for default value
Created a new partition 1 of type 'Linux filesystem' and of size 59.3 GiB.
Command (m for help): w
The partition table has been altered.
```
4. Format the partition as ext4
```
root@smarc-rzg2l:~# mkfs.ext4 -L rootfs /dev/mmcblk0p1
```
5. Mount the ext4 partition to "/mnt".
```
root@smarc-rzg2l:~# mount /dev/mmcblk0p1 /mnt
```
6. Extract the file system to the ext4 partition

### MistySOMG2L
```
root@smarc-rzg2l:~# cd /home/root
root@smarc-rzg2l:~# tar -zxvf core-image-weston-smarc-rzg2l.tar.gz -C /mnt
root@smarc-rzg2l:~# cp Image /mnt/boot
root@smarc-rzg2l:~# cp r9a07g044l2-smarc.dtb /mnt/boot
root@smarc-rzg2l:~# sync
```
### MistySOMV2L
```
root@smarc-rzg2l:~# cd /home/root
root@smarc-rzg2l:~# tar -zxvf core-image-weston-smarc-rzv2l.tar.gz -C /mnt
root@smarc-rzg2l:~# cp Image /mnt/boot
root@smarc-rzg2l:~# cp r9a07g054l2-smarc.dtb /mnt/boot
root@smarc-rzg2l:~# sync
```
7. Unmount “/mnt”
```
root@smarc-rzg2l:~# umount /mnt
```
8. Reboot and change the boot parameter in u-boot to boot from eMMC
```
=> setenv bootargs 'rw rootwait earlycon root=/dev/mmcblk0p1'
=> setenv bootcmd 'ext4load mmc 0:1 0x48080000 boot/Image; ext4load mmc 0:1 0x48000000 boot/r9a07g044l2-smarc.dtb; booti 0x48080000 - 0x48000000'
=> saveenv
=> boot
```
