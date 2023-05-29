<img src="files/img/2018_MistyWest_LogoCombo_FINAL_RGB.png" alt="MistyWest" width="400"/><div style="text-align: right">back to [index](../README.md)</div>

# Reading from ADXL345 3-axis Accelerometer Using i2c Interface  on MistySOM and MistyCarrier 

# Introduction

The purpose of this tutorial is to demonstrate using the MistySOM Carrier board with a mezzanine MistySOM module can be used to read data over the i2c bus on the RZ/G2L from an [ADXL345](https://www.sparkfun.com/products/9836) i2c accelerometer connected to the RIIC1 i2c port on the MistyCarrier J4 connector on pins 7 and 9.  The ADXL345 is a small, thin, low power, 3-axis MEMS accelerometer with high resolution (13-bit) measurement at up to +/-16 g. Digital output data is formatted as 16-bit two's complement and is accessible through either an SPI (3- or 4-wire) or I2C digital interface. 



| ![MH11_CAN_test_setup.jpg](files/img/MH11_CAN_Test_Setup2.jpg) |
| ------------------------------------------------------------ |
| Fig. 1 - Test Setup for  for reading and writing to CanBus from MistySOM RZ/G2L |



| ![MH11_CAN_test_setup1.jpg](files/img/MH11_CAN_Test_Setup1.jpg) |
| ------------------------------------------------------------ |
| Fig. 2 - Test Setup for reading and writing to CanBus from MistySOM RZ/G2L |





## 

## Materials:

**MistyWest MistySOM EVK Kit** (based on  a MistySOM module using a Renesas RZ/G2L or RZ/V2L microcontroller  as mezzanine module mounted on MistyCarrier). Make sure the MistySOM board boots from a micro SD Card running the latest MistySOM-specific RZ/G2L or RZ/V2L V3.0.x  Linux image. A standard break-away 0.1" 2x20-pin dual male header should be soldered to the MistyCarrier J4 i/o pads if not populated. 

**SparkFun Triple Axis Accelerometer Breakout module** part no SEN-09836  (https://www.sparkfun.com/products/9836). See close-up in Fig. 2 below.

| ![ADXL345_Breakout_Hardware.jpg](files/img/ADXL345_Breakout_Hardware.jpg) |
| ------------------------------------------------------------ |
| Fig. 2 - Close up of SparkFun Triple Axis Accelerometer Breakout module using Analog ADXL345 accelerometer |

The Analog Devices ADXL345 accelerometer as used on this SparkFun breakout module supports the following features:

- Supply Voltage: 2.0 - 3.6 VDC
- Ultra Low Power: As low as 23 uA in measurement mode, 0.1uA in standby mode at 2.5V
- SPI or I2C Communication
- Single Tap / Double Tap Detection
- Activity / Inactivity Sensing
- Free-Fall Detection

The ADXL345 is well suited to measure the static acceleration of gravity in tilt-sensing applications, as well as dynamic acceleration resulting from motion or shock. Its high resolution (4 mg/LSB) enables measurement of inclination changes less than 1.0°. Furthermore, low power modes enable intelligent motion-based power management with threshold sensing and active acceleration measurement at extremely low power dissipation.  The ADXL345 accelerometer supports both I2C and SPI bus interfaces.  For this application note, we will use the SparkFun ADXL345 breakout module with an i2c interface.

**Standard +3.3V FTDI TTL-232R-3V3-2MM USB to UART serial cable** (https://ftdichip.com/products/ttl-232r-3v3/) connecting the 1x8-pin keyed J40 connector on the MistyCarrier to a USB 2.0 Type-A port on a host Linux or Windows PC.

![TTL-232R-3V3](files/img/TTL-232R-3V3.jpg)

**Terminal emulator software** running on host PC for a serial terminal console session  (e.g picocom, minicom on a Linux PC or puTTY, TeraTerm on a Windows PC).

**Breadboard** - Standard 0.1" solderless electronic prototyping breadboard with 400 or 800 tie-points with bus strip rows for power and ground rails. 

**Jumper Wires** - standard F/F jumper wires with female connectors on both ends. Use these to jumper from any male header on the MistyCarrier board to any other pin on the prototyping breadboard. The LED toggle procedure verifies one  GPIO pin at a time connected by a jumper to a single pin on header J4 (see Fig 3-4 below) or else a pin on from one of test points TP39-TP66 on the debug pads of the MistyCarrier (see Fig 6-9 below).

**Jumper Pins** - assorted 0.1" breakaway rectangular jumper pins for connecting jumpers to breadboard

### Circuit to read SparkFun ADXL345 breakout module from MistySOM / MistyCarrier using I2C

With the ADXL345, either I2C or SPI digital communications are available. In both cases, the ADXL345 operates as a slave device.
For this application note, we will connect the MistyCarrier with a mezzanine RZ/G2L-based MistySOM module to the ADXL345 breakout board using I2C communication.

Refer to Fig 2 below showing the connector soldered to the J4 dual-row 2x20-pin header at the top centre of the image. 
The schematic for the MistySOM signals on this J4 connector is provided in Fig 3, and the mapping of the RZ/G2L Pin Mux functions to the pins is provided in Fig. 4. Finally Fig 5 shows an example of a Fritzing breadboard diagram of connecting the 3.3V J4 header pin 40 (3v3 RZ/G2L GPIO pin P43_3)  via the 330Ω resistor to the LED anode.  The LED cathode is connected indirectly to pin 4 GND on the J4 header via the GND rail on the breadboard. Toggling the LED on and off is covered in the test procedure section further below. For testing GPIO pin P43_3 on J4 pin 40, the corresponding gpio_J4_pin_40_toggle.sh script is used to toggle the LED on and off 3 times at 1-second intervals.  For testing other GPIO signals on the J4 header, the jumper should be reconnected to the desired GPIO header pin, and the corresponding gpio_J4_pin_NN_toggle.sh should be invoked from the command line. 

| ![VO-REVAFRONT-20221104_merge_alt](files/img/VO-REVAFRONT-20221104_merge_alt.png) |
| ------------------------------------------------------------ |
| Fig 2 - MistyCarrier with mezzanine MistySOM showing J4 2x20-pin header at top center |

| ![MistyCarrier_J4_40-pin_header_schematic](files/img/MistyCarrier_J4_40-pin_header_schematic.png) |
| ------------------------------------------------------------ |
| Fig. 3 - MistyCarrier J4 Connector schematic pinout for pins 1-40 |

| ![MistyCarrier J4 40-pin Header Pinout Mapping to RZ/G2L Pin Mux Functions](files/img/MistyCarrier_J4_Pinout.png) |
| ------------------------------------------------------------ |
| Fig. 4 - Mapping of MistyCarrier J4 Header pinout to Available RZ/G2L pin mux function selection |

I2C mode is enabled if the CS pin is tied to high. There is no default mode if the CS pin is left unconnected, so it should always be tied high or driven by an external controller.

Note: If other devices are connected to the same I2C bus, the nominal operating voltage level of those other devices cannot exceed VDD I/O by more than 0.3 V. External pull-up resistors are necessary for proper I2C operation. Used in this connection diagram are two 4.7 kΩ resistors. Please refer to page 18 of the [ADXL345 Datasheet](https://www.sparkfun.com/datasheets/Sensors/Accelerometer/ADXL345.pdf) for additional information.

The following is a table describing which pins on the Arduino should be connected to the pins on the accelerometer for I2C communication.

| RZ/G2L <br />Signal | MistyCarrier<br />J4 Pin # | **ADXL345 Pin** | Description                                          |
| :------------------ | -------------------------- | --------------- | ---------------------------------------------------- |
| GND                 | 4                          | GND             | This pin must be connected to ground                 |
| 3V3                 | 2                          | VCC             | 3.3V Supply Voltage                                  |
| 3V3                 | 2                          | CS              | Chip Select                                          |
| GND                 | 4                          | SDO             | Serial Data Output (SPI 4-Wire) / I2C Address Select |
| RIIC1_SDA           | 7                          | SDA             | Serial Data I2C / Serial Data Input (SPI 4-WIRE)     |
| RIIC1_SCK           | 9                          | SCL             | Serial Communications Clock                          |



The breadboard circuit image in Fig 5 below shows the SparkFun ADXL345 breakout module connected to the MistyCarrier J4 connector using the RZ/G2L I2C RIIC1 port signals on J4 pins 7 and 9.

| ![ADXL345_I2C_fritzing.jpg](files/img/ADXL345_I2C_fritzing.jpg) |
| ------------------------------------------------------------ |
| Fig. 5 - Fritzing Breadboard Circuit Image of J4 pins 7 and 9 (RIIC1_SDA and RIIC!_SCL) connected to SparkFun ADXL345 accelerometer I2C interface |



## Test Procedure Using SocketCAN and iproute2 tools for RZ/G2L and RZ/V2L



## Purpose

This article describes how to set up a SocketCAN interface using the iproute2[1] suite of tools.

iproute2 is a set of utilities for Linux® networking, integrated in the SDK for the STM32 microprocessors.

## Configuring the SocketCAN interface

You can display available network interfaces to find out the available CAN devices:

```
$ ifconfig -a
can0      Link encap:UNSPEC  HWaddr 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00
          NOARP  MTU:16  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:10
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
          Interrupt:30

eth0      Link encap:Ethernet  HWaddr 00:80:E1:42:45:EC
.
.
.
```

Configure the available SocketCAN interface using the ip link command line as follow:

```
$ ip link set can0 type can bitrate 1000000 dbitrate 2000000 fd on
[ 24.700698] m_can 4400e000.can can0: bitrate error 0.3%
[ 24.704568] m_can 4400e000.can can0: bitrate error 1.6%

```

To list CAN user-configurable options, use the following command line:

```
$ ip link set can0 type can help

Usage: ip link set DEVICE type can
        [ bitrate BITRATE [ sample-point SAMPLE-POINT] ] |
        [ tq TQ prop-seg PROP_SEG phase-seg1 PHASE-SEG1
          phase-seg2 PHASE-SEG2 [ sjw SJW ] ]

        [ dbitrate BITRATE [ dsample-point SAMPLE-POINT] ] |
        [ dtq TQ dprop-seg PROP_SEG dphase-seg1 PHASE-SEG1
          dphase-seg2 PHASE-SEG2 [ dsjw SJW ] ]
    
        [ loopback { on | off } ]
        [ listen-only { on | off } ]
        [ triple-sampling { on | off } ]
        [ one-shot { on | off } ]
        [ berr-reporting { on | off } ]
        [ fd { on | off } ]
        [ fd-non-iso { on | off } ]
        [ presume-ack { on | off } ]
    
        [ restart-ms TIME-MS ]
        [ restart ]
    
        Where: BITRATE  := { 1..1000000 }
                  SAMPLE-POINT  := { 0.000..0.999 }
                  TQ            := { NUMBER }
                  PROP-SEG      := { 1..8 }
                  PHASE-SEG1    := { 1..8 }
                  PHASE-SEG2    := { 1..8 }
                  SJW           := { 1..4 }
                  RESTART-MS    := { 0 | NUMBER }

### 
```

### Displaying SocketCAN status

To get a detailed status of the SocketCAN link, use the following command line:

$ ip -details link show can0
2: can0: <NOARP,ECHO> mtu 72 qdisc pfifo_fast state DOWN mode DEFAULT group default qlen 10
    link/can  promiscuity 0
    can <FD> state STOPPED (berr-counter tx 0 rx 0) restart-ms 0
          bitrate 996078 sample-point 0.745
          tq 19 prop-seg 18 phase-seg1 19 phase-seg2 13 sjw 1
          m_can: tseg1 2..256 tseg2 1..128 sjw 1..128 brp 1..512 brp-inc 1
          dbitrate 2032000 dsample-point 0.720
          dtq 19 dprop-seg 8 dphase-seg1 9 dphase-seg2 7 dsjw 1
          m_can: dtseg1 1..32 dtseg2 1..16 dsjw 1..16 dbrp 1..32 dbrp-inc 1
          clock 50800000numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535

## Enabling/disabling the SocketCAN interface

Then enable the connection by bringing the SocketCAN interface up:

```
$ ip link set can0 up
```

You can check that the interface is up by printing the netlink status:

```
 $ ip -details link show can0
2: can0: <NOARP,UP,LOWER_UP,ECHO> mtu 72 qdisc pfifo_fast state UNKNOWN mode DEFAULT group default qlen 10
    link/can  promiscuity 0
    can <FD> state ERROR-ACTIVE (berr-counter tx 0 rx 0) restart-ms 0
          bitrate 996078 sample-point 0.745
...

```

You can disable the connection by bringing the SocketCAN interface down. This command is useful when you need to reconfigure the SocketCAN interface:

```
 $ ip link set can0 down

## 
```

## Loopback test mode

It is possible to configure the SocketCAN in internal Loopback test mode. In that case, the FDCAN treats its own transmitted messages as received messages. This mode can be used for hardware self-test:

```
 $ ip link set can0 type can bitrate 1000000 dbitrate 2000000 fd on loopback on
```

You can check that loopback option is on by printing the netlink status:

```
 $ ip  -details link show can0
2: can0: <NOARP,ECHO> mtu 72 qdisc pfifo_fast state DOWN mode DEFAULT group default qlen 10
    link/can  promiscuity 0
    can <LOOPBACK,FD> state STOPPED (berr-counter tx 0 rx 0) restart-ms 0
          bitrate 996078 sample-point 0.745

```

...

## Example of SocketCAN interface setup

You can configure and enable the SocketCAN interface in the same command line:

```
$ ip link set can0 up type can bitrate 1000000 dbitrate 2000000 fd on
[ 24.700698] m_can 4400e000.can can0: bitrate error 0.3%
[ 24.704568] m_can 4400e000.can can0: bitrate error 1.6%
[ 24.710140] IPv6: ADDRCONF(NETDEV_CHANGE): can0: link becomes ready

repertoire of socketcan commands

root@smarc-rzg2l:~# history
    1  ifconfig -a
    2  ip link set can1 type can bitrate 125000 dbitrate 250000 fd on
    3  ip link set can1 up
    4  ip -details link show can1
    5  cangen can1
    6  ip link set can1 down
    7  ip -details link show can1
    8  ip link set can1 up
    9  cangen can1
   10  ip link set can1 up
   11  ip -details link show can1
   12  ip link set can1 up
   13  ip -details link show can1
   14* 
   15  candump can1
   16  which candump
   17  ip -d -s link show can1
   18  ip link set can1 down
   19  ip link set can1 type can bitrate 125000 dbitrate 250000 berr-reporting on fd on
   20  ip link set can1 up
   21  ip -details link show can1
   22  fg
   23  jobs
   24  ip link set can1 down
   25  ip link set can1 up
   26  ip -details link show can1
   27  history
   28  cangen can1
   29  ip -details link show can1
   30  cangen can1 &
   31  ip -details link show can1
   32  ip -d -s link show can1
   33  dmesg -n 7
   34  ip -d -s link show can1
   35  dmesg | less
   36  ip link set can1 down
   37  ip link set can1 downjobs
   38  jobs
   39  ip link set can1 type can bitrate 125000 dbitrate 4000000 berr-reporting on fd off
   40  ip link set can1 type can bitrate 125000 berr-reporting on fd off
   41 ip link set can1 type can bitrate 125000 berr-reporting on fd on
   42  ip link set can1 type can bitrate 125000 berr-reporting on
   43  ip link set can1 down
   44  ip -d -s link show can1
   45  ip link set can1 type can bitrate 125000
   46  ip -d -s link show can1
   47  cangen can1 &
   48  ip link set can1 up
   49  cangen can1 &
   50  ip -d -s link show can1
   51  ip link set can1 type can bitrate 125000 berr-reporting on
   52  jobs
   53  fg 1
   54  ip link set can1 down
   55  ip link set can1 type can bitrate 125000 berr-reporting on
   56  ip link set can1 up
   57  cangen can1
   58  candump can1
   59  ip -d -s link show can1
   60  ip link -help
   61  ip link help
   62  candump -h
   63  candump -c -c -x -e -ta can1
   64  canfdtest
   65  canfdtest -vv can1
   66  canfdtest 
   67  canfdtest -g -vv can1
   68  tmux
   69  cangen can1 &
   70  candump can1
   71  jobs
   72  fg 1
   73  canbusload can1
   74  canbusload can1@125000 -r -t -b -c
   75  canbusload
   76  canbusload can1@125000 -r -t -b -c -e
   77  ip -d -s link show can1
   78  history


## 


```

## References

IPROUTE2 information, by The Linux Foundation