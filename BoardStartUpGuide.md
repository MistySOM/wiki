<img src="img/2018_MistyWest_LogoCombo_FINAL_RGB.png" alt="MistyWest" width="400"/><div style="text-align: right">back to [index]( README.md)</div>

# Board Start-Up Guide MistySOM
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

### Booting Flash Writer
1. Set the boot mode to SCIF. On the MistySOM Carrier board, on SW2, set BOOT1 OFF and Boot2  to ON position.
2. Connect the `FTDI cable` to `J40` on the MistyCarrier board and a USB port on the computer
3. Start TeraTerm and select the `COMn: USB Serial Port (COMn)` interface.
4. Set the Baud rate under `Setup -> Serial Port... -> Speed:` to `115200` and click the `New Setting` button.
5. Power on the MistySOM bord.
6. Upon application of power, you should see the following in your terminal window:   
```SCIF Download mode
  (C) Renesas Electronics Corp.
-- Load Program to SystemRAM ---------------
please send !  
```  
7. Select `File -> Send File...` and select the file `Flash_Writer_SCIF__.mot`. A transfer dialog will appear displaying the progress of the file transfer over the setial connection. <br/>
After succesful downlaoding the vbinary, Flash Writer strarts autimatically and shows a message like belowe on the terminal
```
Flash writer for RZ/V2 Series V1.02 Nov.15,2021
 Product Code : RZ/V2L
>
```

### Writing Bootloader

For the boot operation, two boot loader files need to be written to the target board.
```
>XLS2
===== Qspi writing of RZ/G2 Board Command =============
Load Program to Spiflash
Writes to any of SPI address.
Micron : MT25QU512
Program Top Address & Qspi Save Address
===== Please Input Program Top Address ============
Please Input : H'11E00
===== Please Input Qspi Save Address ===
Please Input : H'00000
Work RAM(H'50000000-H'53FFFFFF) Clear....
please send ! ('.' & CR stop load)
```
Send the data of “bl2_bp-smarc-rzv2l_pmic.srec” from terminal software after the message “please send !” is shown.

After successfully download the binary, messages like below are shown on the terminal.
```
SPI Data Clear(H'FF) Check :H'00000000-0000FFFF Erasing..Erase Completed
SAVE SPI-FLASH.......
======= Qspi Save Information =================
SpiFlashMemory Stat Address : H'00000000
SpiFlashMemory End Address : H'00009A80
===========================================================
```

```
SPI Data Clear(H'FF) Check : H'00000000-0000FFFF,Clear OK?(y/n)
```
In case a message to prompt to clear data like above, please enter “y”.


Next, write another loader file by using XLS2 command again.
```
>XLS2
===== Qspi writing of RZ/G2 Board Command =============
Load Program to Spiflash
Writes to any of SPI address.
Micron : MT25QU512
Program Top Address & Qspi Save Address
===== Please Input Program Top Address ============
Please Input : H'00000
===== Please Input Qspi Save Address ===
Please Input : H'1D200
Work RAM(H'50000000-H'53FFFFFF) Clear....
please send ! ('.' & CR stop load)
```
Send the data of “fip-smarc-rzv2l_pmic.srec” from terminal software after the message “please send !” is shown.

Send the data of “fip-smarc-rzv2l_pmic.srec” from terminal software after the message “please send !” is shown.
```
SPI Data Clear(H'FF) Check :H'00000000-0000FFFF Erasing..Erase Completed
SAVE SPI-FLASH.......
======= Qspi Save Information =================
SpiFlashMemory Stat Address : H'0001D200
SpiFlashMemory End Address : H'000CC73F
```

```  
SPI Data Clear(H'FF) Check : H'00000000-0000FFFF,Clear OK?(y/n)
```
In case a message to prompt to clear data like above, please enter “y”.


After writing two loader files normally, turn off the power of the board and set SW2 into QSPI boot mode:
* BOOT1 ON
* BOOT2 OFF
