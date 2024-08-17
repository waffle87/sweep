# Sweep

A [Ferris Sweep](https://github.com/davidphilipbarr/Sweep) variant
designed with an integrated RP2040 and some other added features.

![top](img/pcb_top.svg)
![bottom](img/pcb_bottom.svg)

## Comments & Modifications
This PCB has the exact same dimensions and mounting points (via
SplitKB Tenting Puck mount) as original Sweep PCB, making it compatible
with most existing cases. Due to the use of USB-C interconnects opposed
to TRRS jacks, this PCB is **not** compatible with "high-profile" cases
or cases that have a cutout for TRRS jacks. This PCB only supports
soldered Choc V1 switches, as it is common to build this keyboard
without a plate. Some other modifications include per-key RGB lighting
and underglow, support for I2C OLED display or (some) SPI displays, and
[Cirque Trackpads](https://www.cirque.com/glidepoint-circle-trackpads)
via an FPC connector/cable. If you have any suggestions, please open an
issue/pull request, or email. There are M2 mounting points intended for
an acrylic cover over the display area or to be used as a holder for
the trackpad. I have not designed one, but would welcome somebody else
to.

## Ordering
The following instructions will be for [JLCPCB](https://jlcpcb.com),
but should generally apply to most PCB manufacturers. If you would like
a plate, the one [here](https://github.com/davidphilipbarr/Sweep/blob/main/Sweep%20v2.2/sweepv2_plate.pro)
can probably be used, although I have not verified this.

1. Upload the Gerber files from [production/gerbers.zip](production/gerbers.zip)
2. Set the following parameters: (and change other things like PCB
quantity and colour as desired)
- **Delivery Format**: Panel by Customer
- **Column**: 2, **Row**: 1
3. For PCB assembly, choose **Economic** PCBA type and **Top Side** for
assembly side. If you would like the LEDs and related components to be
installed by PCBA, choose **Standard** and **Both Sides**.
4. Upload BOM file from [production/bom.csv](production/bom.csv) on the
Bill of Materials page
5. Upload CPL file from [production/positions.csv](production/positions.csv)
as well. Disregard any message like "J7, J8 won't be assembled due to
missing data". These are the display pin headers.
6. Select appropriate components on the following screen by typing in
the shown part number and select it. An effort has been made to choose
as few extended components as possible, but this is not possible for
some.
7. Give a quick look at the component placement and make sure nothing
looks wildly wrong. It is normal for some components to be shown
oriented incorrectly or not quite where they belong. The PCB reviewers
will usually correct this without issue.

### Additional components
For components that are not carried or commonly out of stock at JLCPCB:
| Item | Quantity | Remarks |
|------|----------|---------|
| Cirque Trackpad ([TM035035](https://www.mouser.com/ProductDetail/355-TM0350352024-003)) | 1 or 2 | Also available in 23mm & 40mm diameter |
| FPC Connector ([FH33J-12S-0.5SH](https://www.mouser.com/ProductDetail/798-FH33J12S05SH10)) | 1 per trackpad | Quite doable by hand-soldering |
| FPC Cable ([98266-0118](https://www.mouser.com/ProductDetail/Molex/98266-0118)) | 1 per trackpad | Change length as desired |
| Nice!View Display ([LS011B7DH03](https://nicekeyboards.com/nice-view)) | 1 or 2 | Available at a lower price if comfortable hand-wiring [here](https://www.aliexpress.us/item/3256801622787441.html) | 

## Build Instructions
These steps assume you already have an assembled PCB and are installing
switches, RGB LEDs, displays, and/or other peripherals. If you are
hand-assebling the PCBs with all components, the [interactive BOM](https://html-preview.github.io/?url=https://git.pngu.org/sweep/tree/ibom.html)
will be helpful.

1. Ensure keyboard is functional by flashing firmware if necessary
and testing by shorting switch footprint pins with metal tweezers. This
should output keypresses on the computer.
2. Install RGB LEDs if desired. See below pictures for orientation and
data chain. Please double check orientation, as an incorrectly oriented
LED will result in your keyboard not working. It is wise to test the
LEDs as you install them (eg. install a couple, then plug in keyboard).
The per-key LEDs will need to be installed and working before the
underglow LEDs will work.
<!--- ![per_key_orientation](img/per_key_led.png) --->
<!--- ![underglow_orienation](img/underglow_led.png) --->
<!--- ![led_data_chain](img/led_data_chain.png) --->
3. Install display if desired. If using I2C OLED displays, install
1x4 header in left-most four pins and configure fimware appropriately.
4. If adding a Cirque Trackpad, SPI will be used by default. This means
that footprint R1 on the trackpad needs to be populated with a 4.7k
resistor (refer to datasheet for additional info). If you would rather
use I2C, the two jumpers on the back of each PCB need to be bridged. No
further hardware modification on the trackpad is necessary.

## Firmware
Only support for QMK Firmware has been written at this time, it can
be found [here](https://git.pngu.org/qmk_me/tree/keyboards/sweep).
Enter bootloader mode by holding RESET button and tapping BOOT button.
The PCB supports full-duplex serial communication, VBUS detection for
setting handedness, and RGB data level-shifted to 5v. If you choose to
operate Cirque Trackpad by I2C, adjust `rules.mk` file so that
`POINTING_DEVICE_DRIVERS` is set to `cirque_pinnacle_i2c` and that I2C
is enabled in `halconf.h` file. Similarly, if you are using I2C OLED
displays, ensure `features.quantum_painter` is set to `false`, and
`features.oled` is set to `true`. Current firmware has been written for
LS011B7DH03 display, also known as [Nice!View display](https://nicekeyboards.com/nice-view).
