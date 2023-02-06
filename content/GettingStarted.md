<img src="../files/img/2018_MistyWest_LogoCombo_FINAL_RGB.png" alt="MistyWest" width="400"/><div style="text-align: right">back to [index](../README.md)</div>

# Getting Started with MistySOM

### MistySOM


### 


### Table Of Contents

                                
- [Getting Started with MistySOM](#getting-started-with-mistysom-1)
    - [Introduction](#introduction)
        - [Dependencies](#dependencies)
        - [Why build using Docker](#why-build-using-docker)
        - [Setting Up Host Computer](#setting-up-host-computer)
            - [Ubuntu](#ubuntu)
            - [Debian](#debian)
            - [Fedora](#fedora)
            - [Arch](#arch)
    - [Container usage](#container-usage)
        - [Building and starting the container](#building-and-starting-the-container)
        - [Building the SDK](#building-the-sdk-1)
        - [Installation of the SDK](#installation-of-the-sdk)
        - [Description of advanced container options](#description-of-advanced-container-options)
            - [Building the BSP](#building-the-bsp)
            - [Building the SDK](#building-the-sdk-1)
            - [Output](#output)
            - [Load Files to uSD card](#load-files-to-usd-card)
            - [Note to WSL users](#note-to-wsl-users)
            - [Run time information](#run-time-information)
     - [Develop example application](#develop-example-application)
         - [Hello World](#hello-world)
	     - [Source Files](#source-files)
	         - [Build using the installed SDK](#build-using-the-installed-sdk)
	         - [Build using Yocto](#build-using-yocto)
     - [MistySOM Development Shell](#mistysom-development-shell)
     - [Configure U-Boot](#configure-u-boot)
     - [Connecting the hardware](#connecting-the-hardware)




## Getting Started with MistySOM


## Introduction

This guide gets you started and setup to develop software and/or firmware for MistySOM.

MistyWest provides an environment in a docker container to build the BSP[^2] and a SDK[^3] for both versions of MistySOM. The host computer requires docker to be configured, setup and running. The requirements for setting up the host computer are described in the section **[Setting Up Host Computer](#setting-up-host-computer)**.

### Dependencies

The following instructions have some dependencies, they include:
* A running Linux host
* Installed `docker`, running `dockerd`(docker daemon) and added the current user to the `docker` group to allow execution of docker commands


### Why build using Docker

Each version of Yocto may have unique Host requirements (e.g., Ubuntu 16.04, Ubuntu 18.04). This can be cumbersome during development if you are working on multiple projects at the same time. Dedicated build machines do not seem feasible for multiple reasons, one of which is the cost of hardware. Virtual machines have definite shortcomings as they reserve and virtualize the host hardware and only provide a limited set of resources to the build process.

Docker is a great solution to this problem and provides:



* Lightweight and simple to share configuration and setup files (that allows creation of identical environments)
* A git revision controlled build environment
* Does not require strict allocation of RAM and hard drive memory
* Has very little CPU/IO overhead, does not impact build time
* Containers start and stop in 1-2 seconds
* Can run multiple containers simultaneously
* Build environment is kept isolated from host machine

Docker's efficiency allows multiple containers to be run simultaneously without burdening the host OS.


### Setting Up Host Computer

For building the MistySOM BSP and SDK, a Linux host with running docker daemon is needed. Docker can be installed through a distribution’s packaging manager. Lookup how to install docker and start dockerd for your respective distributions. Some links to find documentation about some common distributions are listed below:


#### Ubuntu

[https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/) 


#### Debian

[https://docs.docker.com/engine/install/debian/](https://docs.docker.com/engine/install/debian/) 


#### Fedora

[https://docs.docker.com/engine/install/fedora/](https://docs.docker.com/engine/install/fedora/) 


#### Arch

[https://docs.docker.com/desktop/install/archlinux/](https://docs.docker.com/desktop/install/archlinux/) 

After docker has been installed and started, you can confirm the functionality of the installation with:


```
$ docker run hello-world
```


If you see:


```
Hello from Docker!
```


it means your installation is working correctly.


## Container usage


### Building and starting the container

Clone the MistySOM/rzv2l or MistySOM/rzg2l repository from GitHub[^1], 
```
$ git clone git@github.com:MistySOM/rzg2l.git
```
or
```
$ git clone git@github.com:MistySOM/rzv2l.git
```
enter the `Build/` directory. 
```
$ cd Build/
```
and execute

```
$ ./build.sh
```


This will download the required files and build the container. To enable caching,create a directory to cache the source packages and object build files.  This directory can be reused for every subsequent container run, so that the files do not need to be downloaded & built multiple times. In the below example, that directory is called `/path/to/dir/`


**Important: the directory needs to have read and write access for uid 1000 and gid 1000**<br/>
_(hint: you can execute `chmod -R 777 /path/to/dir` to grant read, write & execute access for everybody on your system to the directory)_


The owner uid and gid setting can be confirmed with


```
$ ls -na /path/to/dir
```


Which should provide a line that looks like (there will be other lines too):


```
drwxr-xr-x 1 1000 1000    0 Month  # HH:MM ./
```


If the first user on a Linux system was used to create the directory. If uid and gid do not indicate ` 1000 1000`, please read about file permissions and attributes, see [here](https://wiki.archlinux.org/title/File_permissions_and_attributes#Changing_permissions). \
Invoke the container and pass the cache directory like:


```
$ ./run.sh -c /path/to/dir
```


Upon completion of the build, the created image files are copied to the directory `output/`, this directory got created at the path as where `./run.sh` was invoked from. The rootfs, kernel and device tree blob, can now be copied to the destination uSD card, see section **[Output](#output)** below.


### Building the SDK

For development purposes, the SDK can be built with the above container, for this, append the `-s` flag to the `./run.sh` call:


```
$ ./run.sh -c /path/to/dir -s
```


This will automatically build the BSP and the SDK and copy the resulting files into the `output/deploy/sdk/ `directory. 


### Installation of the SDK

To install the SDK, execute it as user root on host, e.g. with sudo like (where` /path/to/` is the location of your extracted MistySOMContainer archive):


```
$ sudo /path/to/out/deploy/sdk/poky-glibc-x86_64-core-image-<image_type>- aarch64-<board_name>-toolchain-3.1.5.sh
```


Upon which, paths can be setup by executing:


```
$ source /opt/poky/3.1.5/environment-setup-aatch64-poky-linux
```


This command has to be invoked every time before the SDK is being used.


### Description of advanced container options


#### Building the BSP



* The script `run.sh` can be supplied with an external path to a directory with `-c /path/to/dir` or `--cpath /path/to/dir` where the Yocto downloads and the built object files can be cached (it requires about 10GB of empty space as of 10/20/2022) so that they do not need to be re-downloaded and re-built for every container run (just resubmit the same directory path).  To allow the container to cache the data, the target directory needs to be writable by uid and gid 1000 (which is the default user id  & group id of the first user on a Linux system, confirm with `id -u`, `id -g `& `ln -n`).
* When no download path is submitted, the container will build the binaries in offline mode, utilizing the data & files that have been downloaded during the container build.


#### Building the SDK

The `run.sh` script in addition can be started with the argument `-s` to auto trigger compilation of the SDK after the initial BSP build has finished. The SDK must not be built by itself, hence the `-s` switch will automatically build the BSP, followed by the SDK.


<table>
  <tr>
   <td>Arg
   </td>
   <td>Description
   </td>
  </tr>
  <tr>
   <td><code>-c</code> <code>--cpath</code> <code>DIRECTORY</code>
   </td>
   <td>Path to download cache directory
   </td>
  </tr>
  <tr>
   <td><code>-n --no</code>
   </td>
   <td>Container will start, setup the environment but not auto issue a build but instead start a dev shell
   </td>
  </tr>
  <tr>
   <td><code>-s --sdk</code>
   </td>
   <td>Container will build the SDK
   </td>
  </tr>
</table>



#### Output

After the compilation finishes, the shell in the container will remain active. The output is available in



* images/BSP: `/home/yocto/rzv_vlp_v3.0.0/out/deploy/images/[board]/`
* SDK: `/home/yocto/rzv_vlp_v3.0.0/out/deploy/sdk/`

The files in the above `images/` directory include:


<table>
  <tr>
   <td><strong>Description</strong>
   </td>
   <td><strong>Filename</strong>
   </td>
  </tr>
  <tr>
   <td>Linux kernel
   </td>
   <td><code>Image-smarc-rzv2l.bin</code>
   </td>
  </tr>
  <tr>
   <td>Device tree blob
   </td>
   <td><code>r9a07g044l2-smarc.dtb</code>
   </td>
  </tr>
  <tr>
   <td>Root filesystem
   </td>
   <td><code>&lt;image-name>-smarc-rzv2l.tar.bz2</code>
   </td>
  </tr>
  <tr>
   <td>Boot Loader
   </td>
   <td><code>bl2_bp-smarc-rzv2l_pmic.srec & fip-smarc-rzv2l_pmic.srec</code>
   </td>
  </tr>
  <tr>
   <td>FlashWriter
   </td>
   <td><code>Flash_Writer_SCIF_RZV2L_SMARC_PMIC_DDR4_2GB_1PCS.mot</code>
   </td>
  </tr>
  <tr>
   <td>SDK
   </td>
   <td><code>poky-glibc-x86_64-core-image-&lt;image_type>-aarch64-&lt;board_name>-toolchain-3.1.5.sh</code>
   </td>
  </tr>
</table>


and the resources can simply be copied to the host with `docker cp  NAME:SRC DST` where `NAME` is the name of the running container that can be retrieved by running `docker ps` on the host.

#### Load files to uSD card
In order to test the newly built BSP, files need to be loaded to an uSD card that MistySOM will boot from. Before the files can be copied, the card has to be prepared accordingly, please follow the instructions on the [Preparing uSD card instructions](preparing_usd.md) page accordingly.
After the uSD card has been prepared, mount the two partitions and copy the following files:
* Linux kernel to the first partition (FAT32):
```
sudo cp /path/to/output/imges/smarc-rzg2l/Image-smarc-rzv2l.bin /path/to/mountpart1/Image
```
* Device tree blob to the first partition (FAT32)
```
sudo cp /path/to/output/imges/smarc-rzg2l/r9a07g044l2-smarc.dtb /path/to/mountpart1/
```
* Root filesystem to the second partition (ext4) 
```
sudo cp /path/to/output/imges/smarc-rzg2l/<image-name>-smarc-rzv2l.tar.bz2 /path/to/mountpart2/
cd /path/to/mountpart2/
tar -xvf <image-name>-smarc-rzv2l.tar.bz2
```
After this, your uSD card is ready to be inserted into the MistyCarrier uSD slot.

#### Note to WSL users:

Make sure to work with files on Linux mounts (avoid use of mounted Windows partitions)


#### Run time information



* `./build.sh` Builds the container image from the Dockerfile and downloads the required files, from the web. 
* `./run.sh` Will start the container image, upon start, the `exec.sh` script is executed from within the container
* `exec.sh` invokes `start.sh` which sets up the Yocto build environment inside the container
* after the environment has been setup, `exec.sh` will invoke the bitbake[^4] commands required to build the binary files


## Develop example application


### Hello World


#### Source Files

Create a file called` helloworld.c` with the following contents:


```
#include <stdio.h>

int main() {
	printf("Hello, World!\n");
	return 0;
}
```

Create a file called `hello.bb`[^5] that contains:


```
DESCRIPTION = "Simple helloworld application"
LICENSE = "MIT"
LIC_FILES_CHKSUM = "file://${COMMON_LICENSE_DIR}/MIT;md5=0835ade698e0bcf8506ecda2f7b4f302"

SRC_URI = "file://myhello.c"

S = "${WORKDIR}"

do_compile() {
	${CC} myhello.c ${LDFLAGS} -o myhello
}

do_install() {
	install -d ${D}${bindir}
	install -m 0755 myhello ${D}${bindir}
}
```


#### Build using the installed SDK

This step requires completion of the previous steps: [Building the SDK](#building-the-sdk-1) and [Installation of the SDK](#installation-of-the-sdk).

After the SDK has been installed, set the paths in your environment by executing:


```
$ sudo /path/to/out/deploy/sdk/poky-glibc-x86_64-core-image-<image_type>- aarch64-<board_name>-toolchain-3.1.5.sh
```


On your host computer.


```
$ $CC $CFLAGS $LDFLAGS helloworld.c -o helloworld
```


The resulting binary file `helloworld` can be copied and run on the MistySOM platform.


#### Build using Yocto

For testing this, the container should be started into a dev shell with:


```
./run.sh -n
```


or


```
./run.sh -c /path/to/dir -n
```


to launch it with the cache directory mounted


#### Source the build environment


```
$ source poky/oe-init-build-env
```

and change  to the directory where the layer is going to be located at

```
$ cd ..
```



#### Create Custom Layer


```
$ bitbake-layers create-layer --priority 3 meta-helloworld
```


Use the `hello.c` and `hello.bb` files from above tp create the following directory structure within the `meta-helloworld/` directory:


```
$ tree meta-helloworld
meta-helloworld
├── conf
│   └── layer.conf
├── COPYING.MIT
├── README
└── recipes-example
    ├── example
    │   └── example_0.1.bb
    └── myhello
        ├── files
        │   └── myhello.c
        └── myhello.bb
```



#### Add new layer to configuration

Execute the following command to add the line  `${TOPDIR}/../meta-helloworld \` to the file `conf/bblayers.conf` (after the `meta-renesas` layer).


```
$ sed -i 's/renesas \\/&\n  ${TOPDIR}\/..\/meta-helloworld \\/' conf/bblayers.conf
```



#### Build MyHello

By invocation of


```
$ bitbake myhello
```


The `myhello` test application will be built utilizing the bitbake server.

With 


```
$ find . -name myhello*
```


The output package can be investigated.

To add the output of this recipe to the output images, add the following to your `conf/local.conf` file in your Yocto build directory:


```
IMAGE_INSTALL_append = " myhello"
```


This will put the myhello app to the rootfs.


## MistySOM Development shell

When to container gets started with the `-n` argument like:

```
./run.sh -n
```
The container image gets started with the Yocto build environment setup. For administrative tasks, the primary user in the container image is called `yocto` with the password set to `yocto`.

## Configure U-Boot 
In order for MistySOM to boot with the files copied onto the uSD card above, the bootloader needs to be configured accordingly. For instructions on how to configure U-Boot, please follow the information on [Configure U-Boot](BoardStartUpGuide.md#configure-u-boot)

## Connecting the hardware

To test the Build generated by following the above instructions, the MistySOM hardware has to be connected and powered up.
For development purposes, use the FTDI serial console cable[^6] and the UISB-C power cable[^7]
Connect the FTDI able to connector `J40` on MistyCarrier and connect the USB-C power cable to `J1`. The locations of the two connectors are hightlighted on the below image: 
<img src="files/img/MistyCarrier_PowerFTDI.png" alt="MistyWest" width="400"/>

Open a serial terminal:([instructions with tera Term for Windows](https://learn.sparkfun.com/tutorials/terminal-basics/tera-term-windows) or [instructions with screen for Linux](https://www.cyberciti.biz/faq/unix-linux-apple-osx-bsd-screen-set-baud-rate/)) and set the baudrate to `115200`bps. when you connected the board succesfully and connected t it in thwe first few seconds while it is booting, you should see boot messages followed by a login prompt:
```
smarc-rzg2l login:
```
If you don't see anything, after a key-press on the `Enter` key, the above login prompt should appear. You can now login with useranme `root` which will bring you to the shell from where you can invoke and execute commands.




<!-- Footnotes themselves at the bottom. -->
## Notes

[^1]:
     [https://github.com/MistySOM/rzv2l](https://github.com/MistySOM/rzv2l) or [https://github.com/MistySOM/rzg2l](https://github.com/MistySOM/rzg2l)
[^2]:
     BSP Board Support Package, also see: [Board Support Package on Wikipedia](https://en.wikipedia.org/wiki/Board_support_package)
[^3]:
     SDK Software Development Kit, also see [Software Development Kit on Wikipedia](https://en.wikipedia.org/wiki/Software_development_kit)
[^4]:
     BitBake, also see [BitBake](https://en.wikipedia.org/wiki/BitBake)
[^5]:
     bb files are bitbake recipes, also see [Recipes in BitBake User Manual](https://docs.yoctoproject.org/1.6/bitbake-user-manual/bitbake-user-manual.html#recipes)
[^6]:
     USB to TTL Serial Cable (3V3): type number: FTDI TTL-232R-3V3-2MM  
[^7]:
     USB-C power cable that is connected to a 5V/3A power brick


