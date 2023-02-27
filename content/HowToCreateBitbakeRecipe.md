<img src="../files/img/2018_MistyWest_LogoCombo_FINAL_RGB.png" alt="MistyWest" width="400"/><div style="text-align: right">back to [index](../README.md)</div>

# How to create a custom bitbake recipe

1. Open a terminal and navigate to the directory where you want to store the recipe.
2. Create a new file with the .bb extension using your preferred text editor. For this example, we'll call the file "desiredapp.bb".
3. Start the recipe by defining the metadata. Here's an example:
```
SUMMARY = "Recipe for DesiredApp"
DESCRIPTION = "Compiles DesiredApp from a public GitHub repository"
LICENSE = "MIT"
LIC_FILES_CHKSUM = "file://${COREBASE}/LICENSE;md5=4d92cd373abda3937c2bc47fbc49d690"
SECTION = "examples"
DEPENDS = "git"

SRC_URI = "git://github.com/user/DesiredApp.git;protocol=https"
SRCREV = "master"
S = "${WORKDIR}/git"

inherit cmake
```
4. In the metadata, the **SRC_URI** parameter defines the URL of the GitHub repository to be cloned. The **SRCREV** parameter specifies which branch or commit to check out. The S parameter specifies the location where the source code will be stored after cloning.
5. The **inherit** parameter tells BitBake which class to inherit from for building the application. In this case, we're using the **cmake** class, which will automatically generate the Makefiles and build the application using the CMake build system.
6. Save and close the file.
7. Add the recipe to your BitBake build environment by including it in your **local.conf** file. To do this, open **local.conf** and add the following line:
```
BBFILES += "/path/to/your/recipe/desiredapp.bb"
