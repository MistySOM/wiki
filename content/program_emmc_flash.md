# Program eMMC flash

* To program the eMMC Flash device on MistySOM, the below instructions can be followed.
* You will need to boot with an SD card first and partition the eMMC Flash device. Then you can load the files from the SD card to eMMC Flash.

1. Boot the board with the SD card and log into Linux.

2. Partition the eMMC Flash using fdisk.

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

3. Copy the currently booted root partition to the newly created partition:
```
root@smarc-rzg2l:~# dd bs=4M status=progress if=/dev/mmcblk1p2 of=/dev/mmcblk0p1
root@smarc-rzg2l:~# sync
```

4. Reboot and change the boot parameter in u-boot to boot from eMMC
   ### RZG2L
```
=> setenv bootargs 'rw rootwait earlycon root=/dev/mmcblk0p1'
=> setenv bootcmd 'ext4load mmc 0:1 0x48080000 boot/Image-smarc-rzg2l.bin; ext4load mmc 0:1 0x48000000 r9a07g044l2-smarc.dtb; booti 0x48080000 - 0x48000000'
=> saveenv
=> boot
```
   ### RZV2L
```
=> setenv bootargs 'rw rootwait earlycon root=/dev/mmcblk0p1'
=> setenv bootcmd 'ext4load mmc 0:1 0x48080000 boot/Image-smarc-rzv2l.bin; ext4load mmc 0:1 0x48000000 r9a07g054l2-smarc.dtb; booti 0x48080000 - 0x48000000'
=> saveenv
=> boot
```
