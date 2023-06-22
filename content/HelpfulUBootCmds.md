---
layout: page
title: "Helpful U-Boot Commands"
---

### Revision History

<table>
  <tr>
   <td>Version
   </td>
   <td>Description of Changes
   </td>
   <td>Date
   </td>
  </tr>
  <tr>
   <td>
	   1.0
   </td>
   <td>
	   Initial Version
   </td>
   <td>
	   12/07/2022
   </td>
  </tr>
  <tr>
   <td>
   </td>
   <td>
   </td>
   <td>
   </td>
  </tr>
</table>


## SDCard & MMC commands

### Confirm SDcard Contents

```
=> mmc list
sd@11c00000: 0 (eMMC)
sd@11c10000: 1
```
### Select Device
```
=> mmc dev 1
switch to partitions #0, OK
mmc1 is current device
```
### List Partitions
```
=> mmc part

Partition Map for MMC device 1  --   Partition Type: DOS

Part    Start Sector    Num Sectors     UUID            Type
  1     2048            67584           a1d1165e-01     0b
  2     69632           62451712        a1d1165e-02     83
```  
### List Files on Partition (`fatls` for fat,  `ext4ls` for ext4 partitions)
```  
=> fatls mmc 1:1
 16955904   Image
    37530   r9a07g044l2-smarc.dtb

2 file(s), 0 dir(s)
```

## Restore Default Configuration

```
env default -a
```

## Variables that need to be Updated to Boot MistySOM

```
bootargs
```

```
bootcmd
```
