<img src="img/2018_MistyWest_LogoCombo_FINAL_RGB.png" alt="MistyWest" width="400"/><div style="text-align: right">back to [index]( README.md)</div>

# Start-Up Guide MistySOM
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
