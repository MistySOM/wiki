# Connect In-Circuit Debugger

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
	   12/12/2022
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


## Requirements

### Software

#### GDB

You need to have built the SDK by following the [Getting Started](/content/GettingStarted.md#building-the-sdk-1) Guide. If you installed the SDK at the default location in `/opt/poky`, we need the `gdb` binary from:<br/>
`/opt/poky/3.1.14/sysroots/x86_64-pokysdk-linux/usr/bin/arm-poky-linux/arm-poky-linux-gdb`

#### OpenOCD

To build OpenOCD you can use this [script](https://github.com/renesas-rz/rzg_openocd/blob/master/build_openocd), e.g.: 
```
$ wget https://raw.githubusercontent.com/renesas-rz/rzg_openocd/master/build_openocd
$ chmod +x build_openocd
$ ./build_openocd
```


## Connect to the Board

### Settings
 
Power cycle the MistyCarrier board, with both switches, **1 & 2** on **SW1** in the **OFF** position. 

## OpenOCD

Start OpenOCD with the following command:<br />
`$ sudo bin/openocd -f share/openocd/scripts/interface/jlink.cfg -c "set SOC G2L" -f share/openocd/scripts/target/renesas_rz_g2.cfg`

and you expect it to start with the following message:
```
Info : JTAG tap: r9a07g044l.cpu tap/device found: 0x6ba00477 (mfg: 0x23b (ARM Ltd), part: 0xba00, ver: 0x6)
Info : r9a07g044l.a55.0: hardware has 6 breakpoints, 4 watchpoints
Info : starting gdb server for r9a07g044l.a55.0 on 3333
Info : Listening on port 3333 for gdb connections
Info : starting gdb server for r9a07g044l.m33 on 3334
Info : Listening on port 3334 for gdb connections
Info : gdb port disabled
```
After it has started as seen above, open a new terminal window to launch the `gdb` binary indicated above under [Requirements](#Requirements).
In the new window launch `gdb` from the installed SDK:
```
$ /opt/poky/3.1.14/sysroots/x86_64-pokysdk-linux/usr/bin/aarch64-poky-linux/aarch64-poky-linux-gdb Flash_Writer_SCIF_RZV2L_SMARC_PMIC_DDR4_2GB_1PCS.mot
```
with the `Flash_Writer` binary that was compiled following the instructions about [building the bootloaders](/content/BuildBootloaders.md).
Upon execution of the above command, a screen similar to the below is expected:
```
GNU gdb (GDB) 9.1
Copyright (C) 2020 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "--host=x86_64-pokysdk-linux --target=aarch64-poky-linux".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from Flash_Writer_SCIF_RZV2L_SMARC_PMIC_DDR4_2GB_1PCS.mot...
(No debugging symbols found in Flash_Writer_SCIF_RZV2L_SMARC_PMIC_DDR4_2GB_1PCS.mot)
```
upon which a connection to the remote target can be established by entering:
```
(gdb) target remote localhost:3333
```
this should be acknowledged in the other window where `openocd` was started with
```
Info : accepting 'gdb' connection on tcp/3333
Info : New GDB Connection: 1, Target r9a07g044l.a55.0, state: halted
```
entering the command
```
gdb) monitor reset init
```
will reset the MPU which can be confirmed by watching the output on the serial console.<br/>
Follow info on [Z-G/RZG2 Eclipse develop and debug](https://renesas.info/wiki/RZ-G/RZG2_Eclipse_develop_and_debug#Cross_debugging_bare_metal_programs_using_GDB_and_OpenOCD) to setup Eclipse with OCD for debugging Flash Writer.
