<img src="../files/img/2018_MistyWest_LogoCombo_FINAL_RGB.png" alt="MistyWest" width="400"/><div style="text-align: right">back to [index](../README.md)</div>

# How to Add Device a Custom Device
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
	   02/08/20233
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

### Introduction

The Prerequisite for the below steps are, that the new device already is supported by the Linux kernel version we are about to modify. To confirm driver support for a specific kernel version, search the [kernelconfig](https://www.kernelconfig.io/) website.
 
To add a custom device to the MistySOM distrution, multiple steps are required. They can be separated as follows:
 
1. Update Linux kernel config to compile the driver for the new device 
2. Patch the device tree to configure the driver for the new device.
3. Rebuild the kernel & device tree
4. Load & boot the rebuilt files for testing


  
