---
title: "Custom Recipes from a Local Source"
---

# How to create a custom bitbake recipe from local sources

Here is an example recipe that you can use:
```
SUMMARY = "Recipe for DesiredApp"
LICENSE = "CLOSED"

SRC_URI = "file://path/to/DesiredApp"

S = "${WORKDIR}"

do_compile() {
    ${CC} ${CFLAGS} ${LDFLAGS} ${WORKDIR}/DesiredApp.c -o ${WORKDIR}/DesiredApp
}

do_install() {
    install -d ${D}${bindir}
    install -m 0755 ${WORKDIR}/DesiredApp ${D}${bindir}
}

FILES_${PN} += "${bindir}/DesiredApp"
```
In the recipe, you will need to replace **path/to/DesiredApp** with the actual path to the source files for your DesiredApp.

Here's a brief explanation of what each section does:

* **SUMMARY** is a short description of the recipe.
* **LICENSE** specifies the license under which the recipe's files are distributed. In this case, I've used CLOSED as a placeholder for a closed-source license.
* **SRC_URI** specifies the location of the source files for your DesiredApp. In this example, I've assumed that the source files are available locally, so I've used file:// to specify the path to the files.
* **S** is a variable that points to the directory where the source files have been extracted.
* **do_compile()** is a function that contains the commands necessary to compile the application.
* **do_install()** is a function that contains the commands necessary to install the application on the target system.
* **FILES_${PN}** specifies which files should be included in the recipe's package. In this case, it includes the binary file DesiredApp in the bindir directory.

Once you have created the recipe, you can add it to your BitBake build by adding the recipe name to your build configuration file, like so:
```
IMAGE_INSTALL_append = " DesiredApp"
```
This will tell BitBake to include the DesiredApp recipe in the image it builds.
