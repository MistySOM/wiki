# Camera Selection

MistySOM is currently supporting different cameras. They require a different device-tree file to be [configured on the U-Boot](/content/BoardStartUpGuide.md#configure-u-boot). All different device-tree files and kernel modules are included inside the boot directory of the MistySOM image.

The following is the list of supported cameras and their related device-tree:

1. **Coral Camera**

    A 5-megapixel camera module that's compatible with the Coral [Dev Board](https://coral.ai/products/dev-board/) and [Dev Board Mini](https://coral.ai/products/dev-board-mini/). Connects through the MIPI-CSI interface, and provides an easy way to bring visual inputs. [Read more here](https://coral.ai/products/camera/)

    - **RZG2L Device-Tree:** r9a07g044l2-smarc.dtb
    - **RZV2L Device-Tree:** r9a07g054l2-smarc.dtb

2. **e-CAM20_CURZ - AR0234 Global Shutter Camera**

    A 2-megapixel custom lens camera module based on 1/2.6" AR0234CS CMOS image sensor from ON Semiconductor®. It is a color camera which supports UYVY image format and provided with S-mount (also known as M12 board lens) lens holder. [Read more here](https://www.e-consystems.com/renesas/ar0234-global-shutter-camera-for-renesas-rz-v2.asp)

    - **RZG2L Device-Tree:** r9a07g044l2-smarc-ar0234.dtb
    - **RZV2L Device-Tree:** r9a07g054l2-smarc-ar0234.dtb

3. **e-CAM221_CUMI462_MOD - Sony® Starvis™ IMX462 Ultra-Lowlight Camera Module**

    It is based on a SONY® STARVIS™ IMX462LQR sensor that ensures superior near-infrared performance. The high sensitivity of this Full HD camera module helps to capture images in as extremely low lighting conditions as 0 lux, which makes it suitable for night vision applications and medical microscopes. [Read more here](https://www.e-consystems.com/camera-modules/sony-starvis-imx462-low-light-camera-module.asp)

    - **RZG2L Device-Tree:** r9a07g044l2-smarc-imx462.dtb
    - **RZV2L Device-Tree:** r9a07g054l2-smarc-imx462.dtb
