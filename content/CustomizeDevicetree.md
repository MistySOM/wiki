# Customize device tree

*Note:The following instructions describes device tree modification for the MistySOM CARRIER and needs to be customized to fit a different hardware platform.*

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
Find the file that contains the device node that needs updating (or the place to add the changes to). To update the pin assignments, `rzg2l-smarc-pinfunction.dtsi` needs to be patched.

1. Create a temporary work directory in with: `$ mkdir ~/tmp`
2. Copy the device tree file that is going to be patched and copy it to `$ ~/tmp`:<br>
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
10. To[Add a custom Yocto Layer](CreateCustomYoctoLayer.md) that applies the patch to the build,
11. Copy the patch to the custom layer (called "meta-mistysom"), follow the following instructions:
    * Open a fresh shell window (while leaving the one where you the patch was created/edited open)
    * In the new shell, cd to the directory where the build container repository (e.g. `$ cd /src/github/rzg2l/Build/`) was cloned to.
    * type `$ docker ps` to find the name of the running container, under `NAMES` look for a name like: `<USER>-rzg2l_vlp_v<VERSION>` or `<USER>-rzv2l_vlp_v<VERSION>`
    * copy the created patch file from the container to the correct location in the meta-mistysom layer: `$ docker cp <USER>-rzg2l_vlp_v<VERSION>:/home/yocto/tmp/0001-mistysom-pin-updates.patch meta-mistysom/recipes-kernel/linux/smarc-rzg2l/` (replace `rzg2l` with `rzv2l` as required)
    * exit from the build container (or close the window with the original shell, where the patch was created/edited)
    * Verify that the contents in `meta-mistysom` have been updated: `$ git status`:
    ```
    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git restore <file>..." to discard changes in working directory)
      (commit or discard the untracked or modified content in submodules)
            modified:   meta-mistysom (modified content)
    ```
    * Confirm that the patch you just created is enabled in `meta-mistysom/recipes-kernel/linux/linux-renesas_\%.bbappend`
    * invoke a container rebuild and re-run the container: `$ ./build.sh; ./run.sh -c <YOUR CACHE DIR> -n`
    * in the running container, follow the instructions provided to invoke a rebuild
    ```
    'cd ~/rzg_vlp_v3.0.0/'
    'source poky/oe-init-build-env'
    'bitbake mistysom-image'
    ```
