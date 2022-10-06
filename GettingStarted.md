<img src="img/2018_MistyWest_LogoCombo_FINAL_RGB.png" alt="MistyWest" width="400"/>

# Getting Started with MistySOM

### MistySOM


### 


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
	   10/05/2022
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



### Table Of Contents


[Getting Started with MistySOM](https://github.com/MistySOM/wiki/edit/master/GettingStarted.md#getting-started-with-mistysom-1)





## Getting Started with MistySOM


## Introduction

This guide gets you started and setup to develop software and/or firmware for MistySOM.

MistyWest provides an environment in a docker container to build the BSP and a SDK for both versions of MistYSOM. The host computer requires docker to be configured, setup and running. The requirements to satisfy are explained in the section **[Setting Up Host Computer](#smartreference=y9bhykzhncl5)**.


### Why build using Docker

Each version of Yocto mah have unique Host requirements (e.g. Ubuntu 16.04, Ubuntu 18.04...). This can be cumbersome during development if you are working on multiple projects at the same time. Dedicated build machines do not seem feasible for multiple reasons, one of which is the cost of hardware where virtual machines have definite shortcomings as they reserve and virtualize the host hardware and only provide a limited set of resources to the build process.

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

For building the MistySOM BSP and SDK, a Linux host with running docker daemon is needed. Docker can be installed through a distribution’s packaging manager. Lookup how to install docker and start dockerd for your respective distributions. Some links to find documentation about some common distributions aer listed below:


#### Debian/Ubuntu

[https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/) 


#### Debian

[https://docs.docker.com/engine/install/debian/](https://docs.docker.com/engine/install/debian/) 


#### Fedora

[https://docs.docker.com/engine/install/fedora/](https://docs.docker.com/engine/install/fedora/) 


#### Arch

[https://docs.docker.com/desktop/install/archlinux/](https://docs.docker.com/desktop/install/archlinux/) 

After docker has been installed and started, you can confirm functionality of the installation with:


```
$ docker run hello-world
```


When you see:


```
Hello from Docker!
```


it means your installation is working correctly.


## Container usage


### Building and starting the container

Download an extract The MistySOMContainer archive, enter the directory and execute


```
$ ./build.sh
```


This will download the required files and build the container. Upon completion of the build,

create a directory to cache the source packages that will get downloaded during the image/SDK build. This directory can be reused for every subsequent container run, so that the files do not need to be downloaded multiple times. In the below example, that directory is called `/path/to/dir/`


```
Important: the directory needs to have read and write access for uid 1000
```


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
$ ./run.sh -d /path/to/dir
```


Upon completion of the build, the created image files are copied to the directory `output/`, this directory got created at the path as where `./run.sh` was invoked from. The rootfs, kernel and device tree blob, can now be copied to the destination uSD card, see section **[Output](#smartreference=ccmxbdlxn17w)** below.


### Building the SDK

For development purposes, the SDK can be built with the above container, for this, append the `-s` flag to the `./run.sh` call:


```
$ ./run.sh -d /path/to/dir -s
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


### Advanced usage description of container options


#### Building the BSP



* The script `run.sh` can be supplied with an external path to a directory with `-d /path/to/dir` or `--dpath /path/to/dir` where the Yocto downloads can be cached (it requires about 7.3GB of empty space as of 10/04/2022) so that they do not need to be re-downloaded for every container run (just resubmit the same path).  To allow the container to cache the data, the target directory needs to be writeable by uid and gid 1000 (which is the default user id  & group id of the first user on a Linux system, confirm with `id -u`, `id -g `& `ln -n`).
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
   <td><code>-d</code> <code>--dpath</code> <code>DIRECTORY</code>
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
   <td><code>r9a07g05l2-smarc.dtb</code>
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


#### Note to WSL users:

Make sure to work with files on Linux mounts (avoid use of mounted Windows partitions)


#### Files

Files that get downloaded by the Dockerfile on build are stored under:

Z:\WebDownload\mh11\rzv2l\VerifiedLinuxPackage_v3.0.0


#### Run time information



* `./build.sh` Builds the container image from the Dockerfile and downloads the required files, from the above resource
* `./run.sh` Will start the container image, upon start, the `exec.sh` script is executed from within the container
* `exec.sh` invokes `start.sh` which sets up the Yocto build environment inside the container
* after the environment has been setup, `exec.sh` will invoke the bitbake commands required to build the binary files


## Develop example application


### Hello World


#### Source File

Create a file called` helloworld.c` with the following contents:


```
#include <stdio.h>

int main() {
	printf("Hello, World!\n");
	return 0;
}
```



#### Build using the installed SDK

This step requires completion of the previous steps: **[Building the SDK](#smartreference=b9t70lsdf2mg) **and **[Installation of the SDK](#smartreference=3n4ql6l1haxs)**.

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


