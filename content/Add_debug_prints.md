<img src="../files/img/2018_MistyWest_LogoCombo_FINAL_RGB.png" alt="MistyWest" width="400"/><div style="text-align: right">back to [index](../README.md)</div>
# Add debug prints to kernel drivers

Modify the Ethernet driver recipe to add debug statements. You can do this by modifying the recipe file for the Ethernet driver. For example, if the Ethernet driver recipe is called linux-ethernet-driver.bb, you can modify it by adding the following line to the end of the file:

`makefile`
```
SRC_URI += "file://ethernet-driver-debug.patch"
```
This will tell Yocto to apply the `ethernet-driver-debug.patch` file as a patch to the Ethernet driver source code during the build process.

Create the `ethernet-driver-debug.patch` file. This file should contain the changes you want to make to the Ethernet driver code to add debug statements. Here's an example patch file that adds a printk() statement to the phy_connect() function in the Ethernet driver code:

`diff`
```
diff --git a/drivers/net/ethernet/my-eth-driver.c b/drivers/net/ethernet/my-eth-driver.c
index 1234567..890abcde 100644
--- a/drivers/net/ethernet/my-eth-driver.c
+++ b/drivers/net/ethernet/my-eth-driver.c
@@ -123,6 +123,7 @@ static int phy_connect(struct net_device *dev)
    struct phy_device *phydev;
    int ret;

+    printk(KERN_DEBUG "My Ethernet Driver: Starting PHY connection\n");
    phydev = phy_connect(dev, PHY_INTERFACE_MODE_RGMII);
    if (IS_ERR(phydev)) {
        dev_err(&dev->dev, "failed to connect to PHY\n");
```
Note that the `@@` line specifies the line number and context of the code that is being changed. In this example, we're adding a printk() statement on line 123 of the `my-eth-driver.c` file.

Build the image. You can build the image using the bitbake command. For example, if your image recipe is called `mistysom-image.bb`, you can build the image by running:

```
$ bitbake mistysom-image
```
Deploy the image. Once the image is built, you can deploy it to your target device and test the Ethernet driver with the debug statements. The debug output should be visible in the kernel log, which you can view using the dmesg command or a log viewer tool.
