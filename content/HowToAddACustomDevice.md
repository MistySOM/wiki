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
 
1. [Update Linux kernel configuration to compile the driver for the new device](#1-update-linux-kernel-configuration-to-compile-the-driver-for-the-new-device)
2. [Patch the device tree to configure the driver for the new device.](#2-patch-the-device-tree-to-configure-the-driver-for-the-new-device)
3. [Rebuild the kernel & device tree](#3-rebuild-the-kernel--device-tree)
4. [Load & boot the rebuilt files for testing](#4-load--boot-the-rebuilt-files-for-testing)


### 1. Update Linux kernel configuration to compile the driver for the new device 

The prerequisite for this step is, that the build container from the repository has been cloned and started with the dev shell argument. To find out more about it, please see [Description of advanced container options](https://github.com/MistySOM/wiki/blob/master/content/GettingStarted.md#description-of-advanced-container-options) in the [Getting Started Guide](https://github.com/MistySOM/wiki/blob/master/content/GettingStarted.md) and start the container with the `-n` argument.
When the container is started, the direcory changed and the build environment sourced, the present directory should be `build/`. From here, 
```
bitbake linux-renesas -c menuconfig
```
 can be invoked to launch `menuconfig`(a `ncruses` based configuration utility) to customize the Linux kernel options. Configuration options can be difficult to find and you can use [kernelconfig.io](https://www.kernelconfig.io/index.html) to find if support for your device is part of the Linux kernel at a selected version. It will also give directions, about where the configuration option can be found and enabled. <br/>After the option has been enabled, save the configuration (default filename `.config` is fine)and exit the menuconfig utility.<br/>
Back on the shell,
```
bitbake linux-renesas -c diffconfig
```
can be invoked to create a fragment of the configuration differences. Copy the fragment from `$WORK/build/tmp/work/smarc-rz_rzv2l-poky-linux/linux-renesas/[kernelversion...]/` to `$WORK/meta-renesas/recipes-common/recipes-kernel/linux/linux-renesas/` and rename it for ease of management (e.g. `MY_DEVICE.cfg`).
if you look at the contents of your `MY_DEVICE.cfg` it should look similar to:
```
cat $WORK/meta-renesas/recipes-common/recipes-kernel/linux/linux-renesas/MY_DEVICE.cfg 
CONFIG_TYP_MY_DEVICE=y
```
After the fragment has been added to, edit the kernel bitbake recipe located at `$WORK/meta-renesas/recipes-common/recipes-kernel/linux/linux-renesas_#.##.bb` and add `MY_DEVICE.cfg` to the configuration fragments listed under `SRC_URI_append = " \` like so:
```
SRC_URI_append = " \
file://MY_DEVICE.cfg \ (new)
" 
```
invocation of 
```
bitbake mistysom-image
```
will now build the kernel with the newly added device driver included.



### 2. Patch the device tree to configure the driver for the new device.

An excellent tutorial that relates to editing the device tree, can be found here: [Intro to Embedded Linux Part 5 - How to Enable I2C in the Yocto Project](https://www.digikey.com/en/maker/projects/intro-to-embedded-linux-part-5-how-to-enable-i2c-in-the-yocto-project/6843bbf9a83c4c96888fccada1e7aedf). <br/>
Also see this article: [Customize Device tree](CustomizeDevicetree.md)



### 3. Rebuild the kernel & device tree

Invoke 
```
bitbake virtual/kernel -c compile -f && bitbake virtual/kernel
```
to re-compile the kernel and the device tree



### 4. Load & boot the rebuilt files for testing

Prepare a uSD card according to instructions [here](preparing_usd.md), copy the Linux kernel and the device tree blob (.dtb) files to the first partition (see [here](https://github.com/MistySOM/wiki/blob/master/content/GettingStarted.md#output) for details about the output files) and extract the root filesystem to the second partition.


  
