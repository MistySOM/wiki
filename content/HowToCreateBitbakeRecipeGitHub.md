<img src="../files/img/2018_MistyWest_LogoCombo_FINAL_RGB.png" alt="MistyWest" width="400"/><div style="text-align: right">back to [index](../README.md)</div>

# How to create a custom bitbake recipe from a public repository

1. Open a terminal and navigate to the directory where you want to store the recipe. We assume that the application to add is called **DesiredApp**(adjust the name accordingly).
2. Create a new file with the .bb extension using your preferred text editor. For this example, we'll call the file "desiredapp.bb".
3. Start the recipe by defining the metadata. Here's an example:
```
SUMMARY = "Recipe for DesiredApp"
DESCRIPTION = "Compiles DesiredApp from a public GitHub repository"
LICENSE = "MIT"
LIC_FILES_CHKSUM = "file://LICENSE;md5=4d92cd373abda3937c2bc47fbc49d690"
SECTION = "examples"

SRC_URI = "git://github.com/user/DesiredApp.git;protocol=https"
SRCREV = "master"
S = "${WORKDIR}/git"

inherit cmake
```
4. In the metadata, the **SRC_URI** parameter defines the URL of the GitHub repository to be cloned. The **SRCREV** parameter specifies which branch or commit to check out. The S parameter specifies the location where the source code will be stored after cloning.
5. The **inherit** parameter tells BitBake which class to inherit from for building the application. In this case, we're using the **cmake** class, which will automatically generate the Makefiles and build the application using the CMake build system.
6. The **COREBASE** variable is a predefined variable in BitBake that contains the path to the top-level directory of the OpenEmbedded-Core layer. 
7. Save and close the file.
8. Add the recipe to your BitBake build environment by including it in your **local.conf** file. To do this, open **local.conf** and add the following line:
```
BBFILES += "/path/to/your/recipe/desiredapp.bb"
8. Save and close **local.conf**.
9. Build the recipe using the **bitbake** command:
```
bitbake desiredapp

```
This will download the source code from the GitHub repository, build the application using CMake, and create a package containing the compiled binaries. The package will be stored in the **tmp/deploy/** directory of your BitBake build environment.