# Sweep

A [Ferris Sweep](https://github.com/davidphilipbarr/Sweep) variant
designed with an integrated RP2040 and some other added features.

![keyboard_image](img/image1.jpg)
![top](img/pcb_top.svg)

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
issue/pull request, or email.

## Ordering
The following instructions will be for [JLCPCB](https://jlcpcb.com),
but should generally apply to most PCB manufacturers. If you would like
a plate, the one [here](https://github.com/davidphilipbarr/Sweep/blob/main/Sweep%20v2.2/sweepv2_plate.pro)
can probably be used, although I have not verified this.

1. Upload the Gerber files from [production/gerbers.zip](production/gerbers.zip)
2. Set the following parameters: (and change other things like PCB
quantity and colour as desired)
- **Different Design**: 2
3. For PCB assembly, choose **Economic** PCBA type and **Top Side** for
assembly side. If you would like the LEDs and related components to be
installed by PCBA, choose **Standard** and **Both Sides**.
4. Upload BOM file from [production/bom.csv](production/bom.csv) on the
Bill of Materials page
5. Upload CPL file from [production/positions.csv](production/positions.csv)
as well. Disregard any message like "J7, J8 won't be assembled due to
missing data". These are the display pin headers.
- Choose "File Provided as Complete File, proceed with my own files"
6. Select appropriate components on the following screen by typing in
the shown part number and select it. An effort has been made to choose
as few extended components as possible, but this is not possible for
some. Note, footprints D1 & D2 are power indication LEDs. They can be
left unpopulated if you'd rather not have an LED that is always lit.
If you would like them installed while not being blinded, try to find
an LED with a lower forward current and voltage (supply is 5v),
perhaps something in the realm of 2v and 1-5mA. Note number 2, the
current-smoothing capacitors for the RGB LEDs are not required for the
LEDs to function.
7. Give a quick look at the component placement and make sure nothing
looks wildly wrong. It is normal for some components to be shown
oriented incorrectly or not quite where they belong. The PCB reviewers
will usually correct this without issue.

### Additional components
For components that are not carried or commonly out of stock at JLCPCB:
| Item | Quantity | Remarks |
|------|----------|---------|
| Cirque Trackpad ([TM035035](https://www.mouser.com/ProductDetail/355-TM0350352024-003)) | 1 or 2 | Also available in 23mm & 40mm diameter |
| FPC Connector ([FH33J-12S-0.5SH](https://www.mouser.com/ProductDetail/798-FH33J12S05SH10)) | 1 per trackpad | Not terribly difficult to do by hand-soldering |
| FPC Cable ([98266-0118](https://www.mouser.com/ProductDetail/Molex/98266-0118)) | 1 per trackpad | Change length as desired |
| Nice!View Display ([LS011B7DH03](https://nicekeyboards.com/nice-view)) | 1 or 2 | Available at a lower price if comfortable hand-wiring [here](https://www.aliexpress.us/item/3256801622787441.html) |
| Acrylic Cover ([cover.dxf](cover.dxf)) | 1 or 2 | Each uses 4x ~4mm M2 screws and 2x ~4mm M2 standofffs. Unfortunately due to the limited space, the cover can only be used **without** a display or trackpad |

## Build Instructions
These steps assume you already have an assembled PCB and are installing
switches, RGB LEDs, displays, and/or other peripherals. If you are
hand-assebling the PCBs with all components, the [interactive BOM](https://html-preview.github.io/?url=https://github.com/waffle87/sweep/blob/master/ibom.html)
will be helpful.

1. Ensure keyboard is functional by flashing firmware if necessary
and testing by shorting switch footprint pins with metal tweezers. This
should output keypresses on the computer.
2. Install RGB LEDs if desired. The chopped-off corner should align with
the marked corner on PCB - this applies to both per-key and underglow
LEDs. Please double check orientation, as an incorrectly oriented LED
will result in keyboard not working. It is wise to test the LEDs as you
install them (eg. install a couple, then plug in keyboard). The per-key
LEDs will need to be installed and working before the underglow LEDs
will work.
![orienation](img/led_orientation.jpg)
3. Install display(s) if desired. If using I2C OLED displays, install
1x4 header in left-most four pins and configure fimware appropriately.
4. If adding a Cirque Trackpad, SPI will be used by default. This means
that footprint R1 on the trackpad needs to be populated with a 4.7k
resistor (refer to datasheet for additional info). If you would rather
use I2C, the two jumpers on the back of each PCB need to be bridged and
the R1 resistor on trackpad unpopulated. Configure firmware appropriately.
Connect FPC cable to connector on PCB, and to connector on trackpad.

## Firmware
Only support for QMK Firmware has been written at this time, it can
be found [here](https://git.pngu.org/qmk_me/tree/keyboards/sweep).
Enter bootloader mode by holding BOOT button and tapping RESET button.
The PCB supports full-duplex serial communication, VBUS detection for
setting handedness, and RGB data level-shifted to 5v. If you choose to
operate Cirque Trackpad by I2C, adjust `rules.mk` file so that
`POINTING_DEVICE_DRIVERS` is set to `cirque_pinnacle_i2c` and that I2C
is enabled in `halconf.h` file. Similarly, if you are using I2C OLED
displays, ensure `features.quantum_painter` is set to `false`, and
`features.oled` is set to `true` in `keyboard.json` file. Current
firmware has been written for LS011B7DH03 display, also known as
[Nice!View display](https://nicekeyboards.com/nice-view). It is rather
experimental at this time, as this display is not officially supported
by QMK.
