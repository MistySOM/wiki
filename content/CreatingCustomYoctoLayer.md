<img src="files/img/2018_MistyWest_LogoCombo_FINAL_RGB.png" alt="MistyWest" width="400"/><div style="text-align: right">back to [index]( README.md)</div>

# Creating a custom Yocto Layer and Image

## Introduction
This tutorial explains how a custom Yocto layer and a custom bitbake image can be created. It's only for display purposes and should not be used & deployed as is as setting the root password as shown below, is insecure.

1. cd to Yoctro dir
2. init env:<br/>
`$ source poky/oe-init-build-env`
3. leave build dir, do not change it, it's volatile:<br/>
`$ cd ..`
4. create custom layer:<br/>
`$ bitbake-layers create-layer meta-custom`
5. enter new layer directory:<br/>
`$ cd meta-mistysom`
6. create directory structure for custom image inside new recipe:<br/>
`$ mkdir -p recipes-core/images` (naming is important here)
7. Copy "core-image-minimal" recipe as template to customize:<br/>
`$ cp ../poky/meta/recipes-core/images/core-image-minimal.bb recipes-core/images/custom-image.bb`
8. Open `recipes-core/images/custom-image.bb` for customization:<br/>
`$ vi recipes-core/images/custom-image.bb`
9. Edit the file to look as depicted below:

```
SUMMARY = "My custom Linux image."

IMAGE_INSTALL = "packagegroup-core-boot ${CORE_IMAGE_EXTRA_INSTALL}"

IMAGE_LINGUAS = " "

LICENSE = "MIT"

inherit core-image
inherit extrausers

#Set rootfs  to 200MiB by default
IMAGE_OVERHEAD_FACTOR ?= "1.0"
IMAGE_ROOTFS_SIZE ?= "294800"

# Change root password (note the capital -P)
EXTRA_USERS_PARAMS = "usermod -P 'toor' root"
``` 
Changes are:
* Changed `SUMMARY` changed to descripe the custom image
* Added `inherit extrausers` to haave access to the `usermod` command
* Added `IMAGE_OVERHEAD_FACTOR`, for more details, see [here](https://docs.yoctoproject.org/1.5.3/ref-manual/ref-manual.html#var-IMAGE_OVERHEAD_FACTOR)
* Changed `IMAGE_ROOTFS_SIZE` to `294800` which equals a rootfs of about 200MiB (it's not exact)
* Added `EXTRA_USERS_PARAMS = "usermod -P 'toor' root` to set the root password to `toor` (as an example - bad security practice, don't deploy!) (to edit the file hit `i` to enter insert mode where the cursor is)
* save the file and exit ([ESC] + `:x`)
10. Return to `build` directory:<br/>
`$ cd ../build/`
11. Modify `bblayers.conf` to add the layer to the build process: <br/>
`$ vi conf/bblayers.conf` and add the path to new layer on the bottom of the list of existing layers:
`${TOPDIR}/../meta-custom \`
* save the file and exit ([ESC] + `:x`)
12. There is a variable assignment that conflicts with the default password and as a result, the login does not work properly. In order to get around this, open `build/local.conf`:<br/>
`vi build/local.conf` and comment out the `EXTRA_IMAGE_FEATURES` line as follows:<br/>( in `vi` search by entering `/EXTRA_IMAGE_FEATURES`, hit `I` to enter insert mode at beginning of line to insert the comment marker:`#`)
`#EXTRA_IMAGE_FEATURES ?= “debug-tweaks”`
* save the file and exit ([ESC] + `:x`)
13. Invoke bitbake to build the custom-image: <br/>
`$ bitbake custom-image` (this will take a while to build & generate the output)
14. Upon completion, the custom images can be found in the output directory at `tmp/deploy/images/smarc-[boardname]`
