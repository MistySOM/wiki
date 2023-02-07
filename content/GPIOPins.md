<img src="../files/img/2018_MistyWest_LogoCombo_FINAL_RGB.png" alt="MistyWest" width="400"/><div style="text-align: right">back to [index](../README.md)</div>

# GPIO Pins

## GPIO Header

The 40 pin GPIO Header on MistySOM can be configured to expose a different functionatlities including IIC, SPI or USART. The below table indicates the functionalities for each pin:

The column **func0** indicates the default mux setting on the pin.

|func5| func1|func0|desc|pin#|pin#|desc|func0|func1|
|-----|------|-----|----|----|----|----|-----|-----|
||||	5V| 1|2|3.3V | |
||||	1.8V	|3|	4|	GND	| |
||||	GND|	5|	6|	1.2V	||
|||RIIC1_SDA|	dedicated IIC|	7|	8|	GND	||
|||RIIC1_SCL|	dedicated IIC|	9|	10|	2.5V	||
||RIIC2_SDA|	P42_3|	3.3V GPIO|	11|	12|	GND	||
||RIIC2_SCL|	P42_4|	3.3V GPIO	|13	|14	|GND		||
|||NMI	|input	|15	|16	|Analog	|ADC_CH0	|
|||			|GND	|17	|18	|Analog	|ADC_CH1	||
|shared with BLE|	SCIF2_TXD	|P48_0	|3.3V GPIO|	19	|20|	Analog|	ADC_CH2	|
|shared with BLE|	SCIF2_RXD	|P48_1	|3.3V GPIO|	21	|22	|Analog|	ADC_CH3	|
|shared with BLE|	SCIF2_CTS	|P48_3	|3.3V GPIO|	23	|24	|Analog|ADC_CH4	|
|shared with BLE|	SCIF2_RTS	|P48_4	|3.3V GPIO|	25	|26	|Analog|	ADC_CH5	|
||||	GND	|27	|28	|Analog	|ADC_CH6	|
||||GND	|29	|30	|Analog	|ADC_CH7	|
||||GND	|31	|32	|GND		
|SCIF3_TXD|		|P0_0	|3.3V GPIO|	33|	34|	3.3V GPIO|	P43_0|RSPI0_CK
|SCIF3_RXD|		|P0_1	|3.3V GPIO|	35|	36|	3.3V GPIO|	P43_1|SPI0_MOSI
|SCIF3_SCK|		|P1_0	|3.3V GPIO|	37|	38|	3.3V GPIO|	P43_2|SPI0_MISO
||||	GND|	39|	40|	3.3V GPIO|	P43_3|	RSPI0_SSL

## GPIO Pads

Further GPIOs are available on MistyCarrier and accessible via through hole pads between the RTC battery holder `BT1` and the MistySOM connector `J38` as show in the picture below:

<img src="../files/img/DebugPads.jpg" alt="MistyCarrier DebugPads"/>

The column **func0** indicates the default mux setting on the pin.

|pad#|desc|func0|func1|func2|func3|
|----|----|----|----|----|----|
|TP39|DISP_DATA0|P7_2|DISP_DATA0|||
|TP40|DISP_DATA1|P8_0|DISP_DATA1|||
|TP41|DISP_DATA2|P8_1|DISP_DATA2|||
|TP42|DISP_DATA3|P8_2|DISP_DATA3|||
|TP43|DISP_DATA4|P9_0|DISP_DATA4|||
|TP44|DISP_DATA5|P9_1|DISP_DATA5|||
|TP45|DISP_DATA6|P10_0|DISP_DATA6|||
|TP46|DISP_DATA7|P10_1|DISP_DATA7|CAN0_TX||
|TP47|DISP_DATA8|P11_0|DISP_DATA8|CAN0_RX||
|TP48|DISP_DATA9|P11_1|DISP_DATA9|||
|TP49|DISP_DATA10|P12_0|DISP_DATA10|||
|TP50|DISP_DATA11|P12_1|DISP_DATA11|||
|TP51|DISP_DATA12|P13_0|DISP_DATA12|||
|TP52|DISP_DATA13|P13_1|DISP_DATA13|||
|TP53|DISP_DATA14|P13_2|DISP_DATA14||IRQ 7|
|TP54|DISP_DATA15|P14_0|DISP_DATA15|||
|TP55|DISP_DATA16|P14_1|DISP_DATA16|||
|TP56|DISP_DATA17|P15_0|DISP_DATA17|||
|TP57|DISP_DATA18|P15_1|DISP_DATA18|||
|TP58|DISP_DATA19|P16_0|DISP_DATA19||IRQ 3|
|TP59|DISP_DATA20|P16_1|DISP_DATA20||IRQ 4|
|TP60|DISP_DATA21|P17_0|DISP_DATA21||IRQ 5|
|TP61|DISP_DATA22|P17_1|DISP_DATA22||IRQ 6|
|TP62|DISP_DATA23|P17_2|DISP_DATA23|||
|TP63|DISP_CLK|P6_0|DISP_CLK|||
|TP64|DISP_DE|P7_1|DISP_DE|||
|TP65|DISP_HSYNC|P6_1|DISP_HSYNC|||
|TP56|DISP_VSYNC|P7_0|DISP_VSYNC|||


