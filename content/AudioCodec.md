<img src="../files/img/2018_MistyWest_LogoCombo_FINAL_RGB.png" alt="MistyWest" width="400"/><div style="text-align: right">back to [index](../README.md)</div>

# Device Tree implementation for the Audio CODEC TLV320AIC23B
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
	   10/21/2022
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
### Introduction

The Audio CODEC is connected on the MistyCarrier as follows:

```
sound {
        compatible = "fsl,imx-audio-tlv320";
        model = "imx-tlv320";
        ssi-controller = <&ssi0>;
        audio-codec = <&codec>;
        audio-routing =
            "MICIN", "Mic Jack",
            "Headphone Jack", "LHPOUT",
            "Headphone Jack", "RHPOUT";
        mux-int-port = <2>;
        mux-ext-port = <5>;
    };
   sound-hdmi {
       compatible = "fsl,imx6q-audio-hdmi",
                "fsl,imx-audio-hdmi";
&audmux {
    status = "okay";
    pinctrl-names = "default";
    pinctrl-0 = <&pinctrl_audmux_sk>;
+};
&i2c3 {
    pinctrl-0 = <&2c3_ins>;
    pinctrl-names = "default";
    clock-frequency = <100000>;

    status = "okay";
    
    codec: tlv320aic23@1a {
        compatible = "ti,tlv320aic23";
        clocks = <&clks 200>;
        clock-frequency = <12000000>;
        reg = <0x1a>;
    };
about clock
        clocks = <&clks 200>;
        clock-frequency = <12000000>;
        
&iomuxc {
    pinctrl-names = "default";
    pinctrl-0 = <&pinctrl_hog>;
    hog {
        pinctrl_hog: hoggrp {
            fsl,pins = <
                /* SD1_DET */
                MX6QDL_PAD_SD4_DAT1__GPIO2_IO09             0x80000000
                /* AUDIO_MCLK */
                MX6QDL_PAD_NANDF_CS2__CCM_CLKO2             0x80000000             
    audmux {
        pinctrl_audmux_sk: audmux-sk {
            fsl,pins = <
                MX6QDL_PAD_KEY_COL0__AUD5_TXC               0x130b0
                MX6QDL_PAD_KEY_ROW0__AUD5_TXD               0x130b0
                MX6QDL_PAD_KEY_COL1__AUD5_TXFS              0x130b0
                MX6QDL_PAD_KEY_ROW1__AUD5_RXD               0x130b0
            >;
        };
    };
&ssi0 {
    fsl,mode = "i2s-slave";
    status = "okay";
};
```
more information: https://community.nxp.com/t5/i-MX-Processors/How-to-enable-TLV320-on-IMX6Q/m-p/304913