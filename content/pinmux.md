# How to set the pin mux

1. Find out Mux position for the respective pin from the datasheet at: [RZ Family / RZ/G, RZ/V Series](../files/datasheets/r01uh0964ej0110-rzg2l-RTK9744L23C01000BE.pdf) and find the corresponding mux position for the desired functionality with the [RZG2L_pinfunction_List](../files/datasheets/RZG2L_pinfunction_List_r1.1.xlsx) spreadsheet.
2. Then preparepinmux function call according to `RZG2L_PORT_PINMUX(A, B, C)` where:
3. * `A` = portnumber (e.g. for `P40_1` it would be `40`)
   * `B` = pin number (e.g. for `P40_0` it would be `0`)
   * `C` = mux position according to hte  abolve linked pinfunction spreadsheet
4. Prepare a `.dts` file to override any previous/existing pin mux settting. Prepare a file with the following example contents:
5.
```
   &pinctrl {
	can0_pins: can0 {
		pinmux = <RZG2L_PORT_PINMUX(10, 1, 2)>, /* TX */
			 <RZG2L_PORT_PINMUX(11, 0, 2)>; /* RX */
	};

	can1_pins: can1 {
		pinmux = <RZG2L_PORT_PINMUX(40, 0, 3)>, /* TX */
			 <RZG2L_PORT_PINMUX(40, 1, 3)>; /* RX */
	};
 };
  ```
  and store the file in `meta-mistysom/recipes-kernel/linux/dts/`
6. Add a line like:
```
SRC_URI += "file://add-vsc8531-ethernet.dts"
```
to `meta-mistysom/recipes-kernel/linux/linux-renesas_%.bbappend`
7. Build amnd use the pins as configured
From: https://codebrowser.dev/linux/linux/include/dt-bindings/pinctrl/rzg2l-pinctrl.h.html
