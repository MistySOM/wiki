# Create a custom Yocto layer

*Note: The following instructions describe device tree modification for the MistySOM CARRIER and need to be customized to fit a different hardware platform.*

## Steps to follow

To create a custom layer, follow the below steps:
1. Enter the build container
2. Enter the mistysom root directory `cd ~/rzv_vlp_v3.0.0/`
3. setup the environment `$ source poky/oe-init-build-env`
4. Exit the build dir `$cd ..`
5. call bitbake-layers to create the custom layer: `$ bitbake-layers create-layer meta-mistysom`
6. Enter the newly created layer dir `$ cd meta-mistysom`
7. Create a dir for the custom recipes `$ mkdir -p recipes-core/images`
8. Copy the core-image-weston recipe `$ cp ../poky/meta/recipes-graphics/images/core-image-weston.bb recipes-core/images/mistysom.bb`
9. Gio back to the build directory `$ cd ../build`
10. Add a new layer to the configuration file `$ vim conf/bblayers.conf`
11. Add `${TOPDIR}/../meta-mistysom \` after the last line, just before the `"`
