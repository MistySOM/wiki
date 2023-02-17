<img src="../files/img/2018_MistyWest_LogoCombo_FINAL_RGB.png" alt="MistyWest" width="400"/><div style="text-align: right">back to [index](../README.md)</div>



## Customizing MistySOM GPIO PinCtrl Bindings
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
	   02/17/2023
   </td>
  </tr>
</table>


### 1   Linux Device Tree

### 1.1   Introduction

Most modern general-purpose computers, like a desktop or laptop, will consist of several peripherals connected to a main processor through a bus such as PCI, USB, etc. An operating system, such as Windows or Linux, running on the computer can discover or learn about the connected peripherals through enumeration. Enumeration is a process through which the OS can enquire and receive information, such as the type of the device, the manufacturer, or the device configuration, about all the devices connected to a given bus. Once the OS gathers information about a device, it can load the appropriate driver for the device. However, this is not the case when it comes to most embedded systems.

In embedded systems, many peripherals are connected to the main processor with busses like I2C, SPI, and UART, which do not support enumeration. Therefore, when an embedded system uses an operating system, such as Linux, it is necessary to convey the hardware configuration of the system, i.e. all the information about the connected peripherals, to the OS in a standard form. The most common way to do this in Linux is to use a device tree. A device tree is a tree data structure that describes the hardware configuration of the system to the Linux operating system. During boot, the Linux kernel will use the information in the device tree to recognize, load appropriate drivers and manage the hardware devices in the system.

This article will help you get started with Linux device trees by introducing the structure of device trees, describing some properties of device trees and showing you how to modify an existing device tree for your custom hardware configuration. 

Perk

Devices which use buses that support enumeration do not need to be included in the device tree. For example, devices that are connected to a USB Host port do not need to be included in the device tree, since the USB bus protocol supports enumeration. Similarly, all devices that are connected to an I2C bus must be included in the device tree, since the I2C bus protocol does not support enumeration.

### Table of Contents

1 Linux Device Tree
1.1Introduction
1.2Device Tree Structure and Properties
1.3Modifying an Existing Device Tree
1.4Pin Multiplexing

### 1.2   Device Tree Structure and Properties

A Linux device tree begins from a root node (i.e. the Tree Root) and will consist of a level of child nodes and one or more levels of children nodes. Each child node represents a hardware component of the micro-processor. For example, in the MistySOM, each child node represents a component of the Renesas RZ/G2L processor, such as the CPU, an I2C peripheral, etc. Each children node represents a sub-component of a child node or a device attached to the child node. For example, in the MistySOM, the PMIC is attached to the I2C0 peripheral bus and appears as a children node under the I2C0 child node. Each node consists of property-value pairs that describe the hardware of the node. Each child node or children node can have only one parent and the root node has no parent. A block diagram of a simple device tree structure is shown in Figure 1.
![image-20230217090409940](/home/gordf/snap/typora/76/.config/Typora/typora-user-images/image-20230217090409940.png)

Figure 1 Block diagram of a simple device tree structure

In the above picture, you can see the parent-child relationship that exists between the child and children nodes. For example, all CPU cores are grouped as children nodes under CPU child node. Similarly, all I2C devices on a given I2C bus are grouped as children under that I2C node

As mentioned above, a parent-child relationship exists between child and children nodes. Although it is counter intuitive to call *parent node* a *child node* and *child node* a *children node*, this was done to match the terminology used in the official device tree documentation.

To express these relationships, the Linux device tree has a specific syntax and structure that should be followed to ensure a device tree works as intended. Let’s look at a simple device tree template (Figure 2) that shows the generic syntax of the root node, child nodes, children nodes and property-value pairs.

![image-20230217085931916](/home/gordf/snap/typora/76/.config/Typora/typora-user-images/image-20230217085931916.png)

Figure 2. Simple Device Tree Template (© Thomas Petazzoni, Device Tree for Dummies)

In the above figure:

- ***node@0\*** and ***node@1\*** are child nodes.
- ***child-node@0\*** and ***child-node@1\*** are children nodes of their respective child nodes.
- ***node1\*** is an easy to remember label of node@1.
- ***a-string-property, a-string-list-property\***, are properties that are used to describe the child and children nodes.
- ***<&node1>\*** is a phandle (i.e. a reference to node1 from node@0).

The official Device Tree Specification can be found [***here\***](https://github.com/devicetree-org/devicetree-specification/releases/tag/v0.2) (this article will refer to information from version v0.2). A more detailed explanation of the device tree structure and basic concepts is available on the [***Device Tree Usage\***](https://elinux.org/Device_Tree_Usage) webpage. Please make sure you are familiar with these two documents before proceeding.

Now that the structure and the syntax of a device tree is clearer, the next question is: what property-value pairs should be used to describe a particular component and where can that information be found? The answer can be found in [***Device Tree Bindings\***](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/plain/Documentation/devicetree/bindings). The properties that are necessary to describe a particular component in the device tree depends on the requirements of the Linux driver, or kernel module, for that component. All the required properties must be supplied to ensure that the Linux kernel recognizes the component properly and loads the appropriate drivers for that component. The Device Tree Bindings for a particular hardware device will give you this information. Therefore, it is essential to find the right Device Tree Bindings information for your component. More supporting documentation about device tree bindings can be found on the [***Device Tree Reference\***](https://elinux.org/Device_Tree_Reference#Bindings) webpage.

The device tree bindings for all the hardware components must be consolidated to form a complete device tree so that the system can function properly. While this may seem like a daunting task, device trees are seldom built from scratch. The next section will discuss modification and reuse of existing device trees.

### 1.3   Modifying an Existing Device Tree

For an embedded Linux system, a device tree is generally a complex data structure requiring several hundred nodes to describe the entire system hardware architecture. Creating a device tree from scratch and validating it can be a daunting and time-consuming task. Therefore, you should look to reuse device tree include files and modify an existing device tree to meet your requirements.

A device tree for the Lesson 2 board can be created by modifying the published device tree from the <link to MistySOM device tree source file here>

The MistySOM Carrier board device tree consists of 6 files:

<describe the MistySOM .dts and .dtsi files here, one-by-one in point form>

Dividing the device tree into separate files helps with code reusability. Since the internal hardware of the MistySOM remains the same irrespective of where it’s used, the device tree corresponding to it can be put into an include file <MistySOM .dtsi file name here>. Then, this file can be used in many device trees that utilize the MistySOM.

When using device tree include files, it is important to understand the status of each node (i.e. after loading the include file, is a given node in an ***okay\*** or a ***disabled\*** state). The main device tree file can then enabled or disable nodes depending on the system hardware. The nodes with the preceding ampersand (&) in the main device tree file are referencing nodes that are already declared in the <MistySOM .dtsi file name here>.

## Device Tree Customization

## Introduction

A Device Tree is a data structure describing a system's hardware. Some hardware is "discoverable" by design (e.g. PCI buses or USB buses) while some is not (notably memory-mapped peripherals). In the latter case, an operating system executable (the OS kernel) is often hard-coded for one device type. To make an operating system portable across different devices, a description of the layout of each supported hardware configuration is required to ensure the correct drivers and configuration are used. The ARM world is very heterogeneous, each SoC vendor and each board vendor wire their hardware a bit differently. In some other architectures (e.g. x86), there is a standard interface between the board firmware (BIOS) and the operating system to communicate the hardware layout - for instance, ACPI on x86. To overcome this lack of hardware description, the ARM Linux Kernel uses device trees as the preferred format of hardware description beginning around kernel version ~3.2. Prior to this change, all details of how the hardware was wired was part of the platform/machine layer and hard-coded in C structs. This became complicated as more and more ARM SoC vendors and boards appeared. More in-depth information is available in the [presentation of Thomas Petazzoni](http://free-electrons.com/pub/conferences/2013/elce/petazzoni-device-tree-dummies/petazzoni-device-tree-dummies.pdf) about device trees.

The device-tree is not only a data structure that describes the SoC's internal memory-mapped peripherals, but it also allows us to describe the whole board. MistyWest creates device trees for each module as well as each carrier board (given that the modules Linux BSP supports device tree, see table below). However, especially if a custom carrier board is built, it is likely that you as a customer need to modify the device tree too. This article provides information on how to do that.

Some of our Linux BSPs make use of device tree enabled Linux kernels. The device trees are part of the Linux kernel source code and located in the *arch/arm/boot/dts/* folder. For instructions on how to obtain the Linux source code for each module, refer to the article "Build U-Boot and Linux Kernel from Source code".

See the module-specific device tree descriptions and additional/intermediate device on the
"Device Tree for Renesas RZ/G2L and RZ/V2L Based Modules".

Typically, a device tree is defined at multiple levels and composed of multiple device tree files. Device tree files (dts and dtsi) may include other device tree files known as includable device tree files (dtsi). In this manner, a board-level device tree file (dts) generally includes a SoC level device tree file (dtsi). To support the modular approach of MistyWest's MistySOM modules, our device tree files usually have three levels of inclusion: carrier board, module and SoC level. This is also reflected in the device tree file names, which are composed by the three levels: *${soc}-${module}-${board}.dtb*

The Linux kernel needs device tree binaries (*.dtb) to boot. These binaries are generated by the device tree compiler from the device tree source files. The compiler is part of the Linux sources and is automatically built if needed. The kernel build system provides the *dtbs* target which compiles all device trees which are compatible with the current kernel configuration. ARCH and CROSS_COMPILE must be set and the kernel must be configured before device tree binaries can be compiled.

```text
make dtbs
```



By specifying a single device tree file, a specific device can be built.

```text
make r9a07g044l2-smarc.dtb
```



## Device Tree Anatomy

Each supported hardware device has a *compatible* string. Along with the compatible property, the device-specific properties need to be specified. These properties are specified in the device tree bindings. The most important properties are compatible, reg, clocks, interrupts, and status. A memory-mapped device (UART in this case) looks like this:
![image-20230217092900796](/home/gordf/snap/typora/76/.config/Typora/typora-user-images/image-20230217092900796.png)

Nodes can be referenced using the ampersand (&) character and the label.

A more detailed description of the device tree data structure can be found at [devicetree.org](https://elinux.org/Device_Tree_Usage).

## Customizing the Device Tree

Before starting the customization please have a look at the exact device tree layout of the module you are using (see below). Then, a straight forward way to start is copying the file of the carrier board level device tree, e.g. by executing the following command from within the kernel source tree:

```text
<need example command>
```



As a next step you need to extend the Makefile. Edit arch/arm/boot/dts/Makefile and insert <example needed for .dtb file>:

```text
<example needed>
```



The command *make dtbs* should now compile also this new device tree binary. We recommend altering only this carrier board level device tree using the techniques below.

The kernel build system writes the combined device tree to the drive, The combined dts file can be handy to debug what the actual device tree file will look like. The combined file is ultimately compiled into the device tree binary representation (*.dtb) which is used by the kernel to boot.

### Overwriting properties

To overwrite a property, the node needs to be referenced using the ampersand character and the label. Later device tree entries overwrite earlier entries (the sequence order of entries is what matters, hence the include order matters). Typically the higher layers (e.g. carrier board device tree) *overwrite* the lower layers (e.g. SoC device tree) since the higher layers include the lower layers at the very beginning.

E.g. for USB controllers which are capable to be device or host (dual-role), one can overwrite the default mode explicitly using the dr_mode property:

```text
&usbdev0 {
    dr_mode = "host";
};
```



### Activating/Deactivating Devices

An important device attribute is the status property. It allows devices to be activated/deactivated. A lot of devices are specified in the SoC level device trees but are disabled by default. By referencing the base node (using the ampersand character and the label), the device can be enabled by any of the layers overwriting the status property.

```text
&uart4 {
    status = "okay";
};
```



### Overwriting nodes

Entire nodes can be overwritten by simply redefining them. Like overwriting properties, latter definitions overwrite earlier definitions.

```text
<example node needed here>
```



### Delete properties or nodes

It is also possible to delete properties or even nodes using */delete-property/* or */delete-node/*. The following example deletes the property defined in the carrier board level device tree <example .dtsi file name needed>:

```text
&uart1 {
    /delete-property/fsl,uart-has-rtscts;
};
```



To delete a node, use its name, e.g.

```text
/delete-node/backlight;
```



### Aliases

The device tree allows some device types to be rearranged using aliases. This is useful for RTCs, for instance, since the first RTC device is used as the primary time source for the system. The primary time source should be assigned to the rtc0 alias (in this example we assign snvsrtc as the primary RTC, which is Vybrids internal RTC):

```text
    aliases {
        rtc0 = &snvsrtc;
        rtc1 = &rtc;
    };
```



### Referencing nodes

If resources of another device are required, a reference is used to connect the two devices. Typically this is used to assign resources such as interrupts, clocks, GPIOs or PWM channels to a device. Depending on the referenced device, a specific amount of parameters (cells) are necessary. The amount is defined in the *-cells* property of the parent device.

#### GPIO

A GPIO specification needs a reference to a GPIO node and one or more cells (arguments). The amount of cells is driver specific. It can be obtained from the device tree binding documentation or by looking at the GPIO controller node (a device which exports GPIO is marked with the `gpio-controller` property). The `#gpio-cells` property defines how many cells are expected. E.g. Vybrid's GPIO controller is defined as follows in vfxxx.dtsi:

```text
gpio1: gpio@4004a000 {
    compatible = "fsl,vf610-gpio";
    reg = <0x4004a000 0x1000 0x400ff040 0x40>;
    gpio-controller;
    #gpio-cells = <2>;
...
```



This means that the GPIO need to be referenced using two cells, e.g.

```text
    cd-gpios = <&gpio1 10 GPIO_ACTIVE_LOW>;
```



This example assigns a single GPIO from the GPIO controller with the label `gpio1` (referenced using the ampersand character &), and passes it the two cells with a value of `10` and `GPIO_ACTIVE_LOW`. The meaning/order of the cells depends on the parent device type. The parent device's device tree binding documentation should contain more information on that.

<Description of Renesas common GPIO cells format here with examples>

#### Interrupt

Also, the interrupt controller specifies the number of cells required. The SoC internal interrupts are already assigned to the peripherals in the SoC level device tree, hence those most often do not need further handling. For external devices often GPIOs are used as an interrupt source. To make GPIO's available as interrupt sources, the GPIO controllers node is also annotated with the *interrupt-controller* property:

```text
gpio1: gpio@40049000 {
    compatible = "fsl,vf610-gpio";
...
    interrupt-controller;
    #interrupt-cells = <2>;
...
```



Interrupts can be assigned in a similar fashion, however, instead of using the linked parent as part of the interrupt specification, the *interrupt-parent* property needs to be used:

```text
interrupt-parent = <&gpio1>;
interrupts = <10 IRQ_TYPE_LEVEL_HIGH>;
```



This example assigns GPIO 10 of the GPIO bank represented by `gpio1` as the interrupt of a device.

### Pinmuxing

Almost all peripherals need signals multiplexed to external pins in order to operate. The Linux kernel introduced the pinctrl subsystem around 3.1/3.2 and has especially become important in the device tree world. The exact workings of pinctrl (muxing, pin configuration etc.) vary quite a bit between different SoC vendors; therefore, device tree bindings are not standardized across our modules. Refer to the module-specific sections below for how to define a pinctrl block for the module you are using.

However, assigning pins to a driver works with standardized bindings. Each pinctrl subnode needs to be assigned to a driver, otherwise, the pinctrl won't apply the settings on its own. How and how many pinctrl groups can be assigned to a device depends on the device driver used. Most drivers are documented in the kernel source under Documentation/devicetree/bindings/ (search using the compatible string). Most drivers do not have specific pinctrl requirements and a default assignment can be made using `pinctrl-0` for the pinctrl reference and a property `pinctrl-names` with the name `"default"`, e.g.:

```text
    pinctrl-names = "default";
    pinctrl-0 = <&pinctrl_mydevice>;
```



To verify that a certain pinctrl has been picked up by the driver and correctly applied, the debug information available via sysfs can be helpful:

```text
# cat /sys/kernel/debug/pinctrl/pinctrl-handles
```



### Device Tree Bindings

The device tree bindings for most supported hardware devices are documented in the kernel source tree inside the folder *Documentation/devicetree/bindings/*. One can also read the latest version of them online at [kernel.org](https://www.kernel.org/doc/Documentation/devicetree/bindings/). However, bindings might have been changed between the actual kernel version used and the one documented online; hence when in doubt, use the documentation in the source tree.

Another source of device tree bindings are those provided by other boards. The device tree folder *arch/arm/boot/dts/* contains a vast amount of supported ARM boards which might make use of device tree bindings for already supported hardware.

## Device Tree for Renesas RZ/G2L and RZ/V2L Modules

<Need links to .DTS files for various RZ/G2L and V2L EVKs are well as Mistysom here

### MistySOM RZ/G2L and RZ/V2L Modules

The MistySOM RZ/G2L and RZ/V2L modules share the same device tree binary. Click on the box to see the current version of the respective device tree file.

![cached image](http://www.plantuml.com/plantuml/proxy?src=https://raw.githubusercontent.com/MistySOM/wiki/Customize_MistySOM_GPIO_Pinctrl/files/DeviceTreeDtsFileHierarchyRzg2lSmarcEVK.txt)

![nocached image](http://www.plantuml.com/plantuml/proxy?cache=no&src=https://raw.githubusercontent.com/MistySOM/wiki/Customize_MistySOM_GPIO_Pinctrl/files/DeviceTreeDtsFileHierarchyRzg2lSmarcEVK_plantuml.txt)



#### Pinmux (Renesas RZ/G2L)

Pin configuration such as pinmux or drive strength is either set by the RZ/G2L pinctrl driver. The SoC level device trees define the base configuration and allow to extend entries through the *iomuxc* label.

To configure a pin, a device tree node inside the pin controller node with the property <example property name needed>. The macros consist of three parts, a prefix, the pad (or ball) name (as used in datasheets) and the alternate function name. Since each pad has multiple alternate functions, there are multiple macros for a single pad, all ending with a different alternate function. It is crucial to select the correct macro for the intended use (e.g. the macro which contains GPIO as an alternate function if the pad is going to be used as a GPIO).

```text
<example node needed here>
```



The last cell of the pin muxing entry need to be provided as a number in the device tree. This last cell contains the pin settings typically in a hexadecimal notation. 

```text
<example pin mux entry needed here>
```

<Need link to markdown for GPIO pinout here>

For further details see <link to Renesas RZ/G2L datasheet reference>.

## Device Tree Overlays

Device Tree Overlays (DTOs) provide a way to modify the overall device tree without re-compiling the complete device tree. Overlays are small pieces or fragments of an entire device tree. They can be added or removed as needed, often enabling/disabling hardware components in the system.

Device Tree Overlays were **introduced in BSP 5**. Therefore this information does not apply to earlier BSPs.

## Device Tree Customization Examples

The examples are collected in a separate article:

<link to Device Tree Customization Example article needed here>
