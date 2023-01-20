<img src="../files/img/2018_MistyWest_LogoCombo_FINAL_RGB.png" alt="MistyWest" width="400"/><div style="text-align: right">back to [index](../README.md)</div>

# Helpful U-Boot commands
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

### Confirm SDcard contents

```
=> mmc list
sd@11c00000: 0 (eMMC)
sd@11c10000: 1
```
### select device
```
=> mmc dev 1
switch to partitions #0, OK
mmc1 is current device
```
### list partitions
```
=> mmc part

Partition Map for MMC device 1  --   Partition Type: DOS

Part    Start Sector    Num Sectors     UUID            Type
  1     2048            67584           a1d1165e-01     0b
  2     69632           62451712        a1d1165e-02     83
```  
### list files on partition (`fatls` for fat,  `ext4ls` for ext4 partitions)
```  
=> fatls mmc 1:1
 16955904   Image
    37530   r9a07g044l2-smarc.dtb

2 file(s), 0 dir(s)
```

## Restore default configuration

```
env default -a
```

## Variables that need to be updated to boot MistySOM

```
bootargs
```

```
bootcmd
```
