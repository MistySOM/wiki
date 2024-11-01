# Build Bootloaders

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
	   12/08/2022
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

## Introduction

In order to boot MistySOM into Linux, it requires to be loaded with bootloaders. 
### Dependencies
The bootloaders are set to be built on a Linux machine. 
Dependencies include the following packages (on Ubuntu distributions, installation of the `build-essentials package will resolve all dependencies):
* installed SDK (see the [Getting Started](/content/GettingStarted.md) Guide on information about how to [build](/content/GettingStarted.md#building-the-sdk-1) and [install](/content/GettingStarted.md#installation-of-the-sdk) the SDK)
* installed git package
* installed make package

The bootloaders are separate per SOM basis, as documented below:
* [MistySOMG2L ]( #MistySOMG2L)
* [MistySOMV2L ]( #MistySOMV2L)

## MistySOMG2L

### Clone Source Code
Change directories to a location from where the source code can be built
```
$ git clone git@github.com:MistySOM/rzg2_bsp_scripts.git
$ git checkout MistySOMG2L
$ git submodule update --init --recursive
$ cd build_scripts/
```


## MistySOMV2L

### Clone Source Code
Change directories to a location from where the source code can be built
```
$ git clone git@github.com:MistySOM/rzg2_bsp_scripts.git
$ git checkout MistySOMV2L
$ git submodule update --init --recursive
$ cd build_scripts/
```
