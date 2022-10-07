# Customize device tree

## dev shell

Start the [build container](https://github.com/MistySOM/wiki/blob/master/GettingStarted.md#getting-started-with-mistysom-1)  with the `-n` [switch ](https://github.com/MistySOM/wiki/blob/master/GettingStarted.md#description-of-advanced-container-options) to launch a MistySOM development shell and follow  the instructions to build a fresh copy of images. 

After the build has completed, the device tree source file is located at `tmp/work-shared/smarc-rzv2l/kernel-source/arch/arm64/boot/dts/renesas/r9a07g054l2-smarc.dts` inside the `build/` directory.
it includes multiple "device tree include" files (`*.dtsi`):
```
#include "r9a07g054l2.dtsi"
#include "rzg2l-smarc-som.dtsi"
#include "rzg2l-smarc-pinfunction.dtsi"
#include "rz-smarc-common.dtsi"
#include "rzg2l-smarc.dtsi"
```
To update the pin assignments, `rzg2l-smarc-pinfunction.dtsi` needs to be patched.

1. Create a temporary work directory in with: `$ mkdir ~/tmp`
2. Copy the device tree file that is going to be patched and copy it to `~/tmp`:<br>
`$ cp ./build/tmp/work-shared/smarc-rzv2l/kernel-source/arch/arm64/boot/dts/renesas/rzg2l-smarc-pinfunction.dtsi ~/tmp/`
3. Change to the directory `$ cd ~/tmp`
4. Create a copy of the just copied device tree include file: `$ cp rzg2l-smarc-pinfunction.dtsi rzg2l-smarc-pinfunction.dtsi.orig`
5. Modify the originally copied file in your favourite editor: `$ vim rzg2l-smarc-pinfunction.dtsi`
6. Create patch with `$ git diff --no-index rzg2l-smarc-pinfunction.dtsi.orig rzg2l-smarc-pinfunction.dtsi > 0001-mistysom-pin-updates.patch`
7. view the patch file `$ vim 000-mistysom-pin-updates.patch` to confirm the changes
8. update the `a` and `b` filenames on the top of the file (to indicate the correct paths) 
9. Change the paths from:
```
--- a/rzg2l-smarc-pinfunction.dtsi.orig
+++ b/rzg2l-smarc-pinfunction.dtsi
```
to:
```
--- a/arch/arm64/boot/dts/renesas/rzg2l-smarc-pinfunction.dtsi
+++ b/arch/arm64/boot/dts/renesas/rzg2l-smarc-pinfunction.dtsi
```
10. [Add a custom Yocto Layer](CreateCustomYoctoLayer.md) that applies the patch to the build
11. Copy the patch to the custom layer (called meta-mistysom in this example)
    * Reurn back into the build directory `$ cd ~/rzv_vlp_v3.0.0/`
    * source the environment (just in case `$ source poky/oe-init-build-env`)
    * leave the build/ directory `$ cd ..`
    * create a recipe dir `$ mkdir -p meta-mistysom`
    * Change into the newly created directory `$ cd meta-mistysom`
    * Create a kernel-patch dir `$ mkdir -p recipes-kernel/linux/`
    * Copy the patch file into the newly created dir `$ cp ~/tmp/0001-example.patch recipes-kernel/linux/`
    * Lookup kernel name `$ oe-pkgdata-util lookup-recipe kernel`<br>
 expected result `linux-renesas`
    * Create append file with kernel name (for any kernel version) `$ vim recipes-kernel/linux/linux-renesas_%.bbappend`
