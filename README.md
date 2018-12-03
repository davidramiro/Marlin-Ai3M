# Anycubic i3 Mega Marlin Firmware with TFT support

This is my slightly customized version of the [Marlin Firmware](https://github.com/MarlinFirmware/Marlin), gratefully based on [derhopp's repo](https://github.com/derhopp/Marlin-with-Anycubic-i3-Mega-TFT) with his remarkable efforts to get the Anycubic i3 Mega TFT screen to work.

Feel free to discuss issues and work with me further optimizing this firmware!

I am running this version on an i3 Mega Ultrabase V3 (for distinction of the different versions, check [this Thingiverse thread](https://www.thingiverse.com/groups/anycubic-i3-mega/forums/general/topic:27064)) with FYSETC TMC2208 V1.2 installed.
Basically, this should work on every Ultrabase version that has no bed leveling sensor and two Z-axis endstops.


## Why use this?

While the i3 Mega is a great printer for it's price and produces fantastic results in stock, there are some issues that are easily addressed:

- Many people have issues getting the Ultrabase leveled perfectly, using Manual Mesh Bed Leveling the printer generates a mesh of the planeness of the bed and compensates for it on the Z-axis for perfect prints without having to level with the screws.
- Much more efficient bed heating by using PID control. This uses less power and holds the temperature at a steady level. Highly recommended for printing ABS.
- Fairly loud fans, while almost every one of them is easily replaced, the stock FW only gives out 9V instead of 12V on the parts cooling fan so some fans like Noctua don't run like they should. This is fixed in this firmware.
- Even better print quality by enabling Linear Advance, S-Curve Acceleration and some tweaks on jerk and acceleration
- Thermal runaway protection: Reducing fire risk by detecting a faulty or misaligned thermistor. 
- Very loud stock stepper motor drivers, easily replaced by Watterott or FYSETC TMC2208. To do that, you'd usually have to flip the connectors on the board, this is not necessary using this firmware.
- Easily start an auto PID tune or mesh bed leveling via the special menu (insert SD card, select special menu and press the round arrow)

## How to flash this?

I provided three different precompiled hex files: One for no modifications on the stepper motor drivers - good for people who didn't touch anything yet, one for boards with TMC2208 installed and where the connectors have been flipped and one with TMC2208 and the connectors in original orientation.

### Choose your precompiled hex:

- Choose the correct hex file:
- For TMC2208 with connectors in original orientation, use `Marlin-AI3M-XXXXXX-TMC2208.hex`
- For TMC2208 with flipped connectors, use `Marlin-AI3M-XXXXXX-TMC2208_flipped.hex`
- If you use the original stepper motor drivers, use `Marlin-AI3M-XXXXXX-stock_drivers.hex`.

### Or compile it yourself:

- Download Arduino IDE
- Clone or download this repo
- In the IDE, under `Tools -> Board` select `Genuino Mega 2560` and `ATmega2560`
- Open Marlin.ino in the Marlin directory of this repo
- Customize if needed and under `Sketch`, select `Export compiled binary`
- Look for the .hex file in your temporary directory, e.g. `.../AppData/Local/Temp/arduino_build_xxx/` (only the `Marlin.ino.hex`, not the `Marlin.ino.with_bootloader.hex`!)

### After obtaining the hex file: 

- Flash the hex with Cura, OctoPrint or similar
- Connect to the printer and send the following commands:
- `M502` - load hard coded default values
- `M500` - save them to EEPROM

## After flashing:

This firmware is perfectly calibrated for my own machine with TMC2208 (1.015V on the axis, 1.152V on the extruder). If you also have these stepper motor drivers and you run them on similar voltages, this firmware should do great prints right after flashing. Nevertheless, a PID tune and especially calibrating the extruder is **highly recommended**. Simply follow the instructions below.

### Calibrating extruder steps

- Preheat the hotend with `M104 S200`
- Use a caliper or measuring tape and mark 120mm (measured downwards from the extruder intake) with a pencil on the filament
- Send `G91`, `G1 E100 F300` and `G90` in that order
- Your extruder will feed 100mm of filament now
- Measure where your pencil marking is now. If it's exactly 20mm to the extruder, it's perfectly calibrated
- If it's less or more than 20mm, add or subtract that value from 100mm, e.g.:
- If you measure 25mm, your result would be 95mm. If you measure 15mm, your result would be 105mm
- Calculate your new value: ( 100mm / actually extruded filament ) * 98.26
- For example, if your markings are at 15mm, you'd calculate: (100/105) * 98.26 = 93.58
- Put in the new value like this: `M92 X80.00 Y80.00 Z400.00 Exxx.xx`, replacing `x` with your value
- Save with `M500`


### PID tuning

- Turn on parts cooling fan, I recommend running it at 70% because of the 12V mod (`M106 S191`)
- Send `M303 E0 S230 C8 U1` to start extruder PID auto tuning
- Wait for it to finish
- Send `M303 E-1 S80 C8 U1` to start heatbed PID auto tuning
- Wait for it to finish
- Save with `M500`, turn off fan with `M106 S0`

### Bonus: Manual Mesh Bed Leveling

If you have issues with an uneven bed, this is a great feature.

- Level your preheated bed as well as you can
- Send `G29 S1`, your nozzle will go to the first calibration position
- Don't adjust the bed itself, only use software from here on:
- Use the onscreen controls or a tool like OctoPrint to lower or raise your nozzle
- If 0.1mm steps are not enough, you can send specific commands down to 0.02mm via those three commands:
- To raise: `G91`, `G1 Z+0.02`, `G90`
- To lower: `G91`, `G1 Z-0.02`, `G90`
- When done, send `G29 S2` and repeat the process for the next command. Continue with `G29 S2`every time.
- After finishing the 25 points, the printer will beep and calculate. After seeing `ok` on the console, enter `M500` to save the mesh to EEPROM
- To ensure your mesh gets used on every print from now on, go into your slicer settings and look for the start GCode
- Look for the Z-homing (either just `G28` or `G28 Z0`) command and insert these two right underneath it:
```
M501
M420 S1
```
- Enjoy never having to worry about an uneven bed again!

## Detailed changes:

- Thermal runaway protection enabled
- TMC2208 configured in standalone mode
- Stepper orientation flipped (you don't have to flip the connectors on the board anymore)
- Linear advance enabled (Off by default. [Research, calibrate](http://marlinfw.org/docs/features/lin_advance.html) and then enable with `M900 Kx`)
- S-Curve Acceleration enabled
- Some redundant code removed to save memory
- Manual mesh bed leveling enabled ([check this link](https://github.com/MarlinFirmware/Marlin/wiki/Manual-Mesh-Bed-Leveling) to learn more about it)
- Heatbed PID mode enabled
- Minor tweaks on default jerk and acceleration
- 12V capability on FAN0 (parts cooling fan) enabled
- No startup beep


## Menu mods by derhopp:

- Subdirectory support: Press the round arrow after selecting a directory
- Special menu in the SD file menu: Press the round arrow after selecting `Special menu`



## About Marlin

<img align="right" src="../../raw/1.1.x/buildroot/share/pixmaps/logo/marlin-250.png" />

Marlin is an optimized firmware for [RepRap 3D printers](http://reprap.org/) based on the [Arduino](https://www.arduino.cc/) platform. First created in 2011 for RepRap and Ultimaker printers, today Marlin drives a majority of the world's most popular 3D printers. Marlin delivers outstanding print quality with unprecedented control over the process.

[![Coverity Scan Build Status](https://scan.coverity.com/projects/2224/badge.svg)](https://scan.coverity.com/projects/2224)
[![Travis Build Status](https://travis-ci.org/MarlinFirmware/Marlin.svg)](https://travis-ci.org/MarlinFirmware/Marlin)
[![Flattr Us!](http://api.flattr.com/button/flattr-badge-large.png)](https://flattr.com/submit/auto?user_id=ErikZalm&url=https://github.com/MarlinFirmware/Marlin&title=Marlin&language=&tags=github&category=software)


### Contributing to Marlin

If you have coding or writing skills you're encouraged to contribute to Marlin. You may also contribute suggestions, feature requests, and bug reports through the Marlin Issue Queue.

Before contributing, please read our [Contributing Guidelines](https://github.com/MarlinFirmware/Marlin/blob/1.1.x/.github/contributing.md) and [Code of Conduct](https://github.com/MarlinFirmware/Marlin/blob/1.1.x/.github/code_of_conduct.md).

### Marlin Resources

- [Marlin Home Page](http://marlinfw.org/) - The latest Marlin documentation.
- [Marlin Releases](https://github.com/MarlinFirmware/Marlin/releases) - All Marlin releases with release notes.
- [RepRap.org Wiki Page](http://reprap.org/wiki/Marlin) - An overview of Marlin and its role in RepRap.
- [Marlin Firmware Forum](http://forums.reprap.org/list.php?415) - Get help with configuration and troubleshooting.
- [Marlin Firmware Facebook group](https://www.facebook.com/groups/1049718498464482) - Help from the community. (Maintained by [@thinkyhead](https://github.com/thinkyhead).)
- [@MarlinFirmware](https://twitter.com/MarlinFirmware) on Twitter - Follow for news, release alerts, and tips. (Maintained by [@thinkyhead](https://github.com/thinkyhead).)

### Credits

Marlin's administrators are:
 - Scott Lahteine [[@thinkyhead](https://github.com/thinkyhead)]
 - Roxanne Neufeld [[@Roxy-3D](https://github.com/Roxy-3D)]
 - Bob Kuhn [[@Bob-the-Kuhn](https://github.com/Bob-the-Kuhn)]
 - Erik van der Zalm [[@ErikZalm](https://github.com/ErikZalm)]

Notable contributors include:
 - Alexey Shvetsov [[@alexxy](https://github.com/alexxy)]
 - Andreas Hardtung [[@AnHardt](https://github.com/AnHardt)]
 - Ben Lye [[@benlye](https://github.com/benlye)]
 - Bernhard Kubicek [[@bkubicek](https://github.com/bkubicek)]
 - Bob Cousins [[@bobc](https://github.com/bobc)]
 - Petr Zahradnik [[@clexpert](https://github.com/clexpert)]
 - Jochen Groppe [[@CONSULitAS](https://github.com/CONSULitAS)]
 - David Braam [[@daid](https://github.com/daid)]
 - Eduardo José Tagle [[@ejtagle](https://github.com/ejtagle)]
 - Ernesto Martinez [[@emartinez167](https://github.com/emartinez167)]
 - Edward Patel [[@epatel](https://github.com/epatel)]
 - F. Malpartida [[@fmalpartida](https://github.com/fmalpartida)]
 - João Brazio [[@jbrazio](https://github.com/jbrazio)]
 - Kai [[@Kaibob2](https://github.com/Kaibob2)]
 - Luc Van Daele [[@LVD-AC](https://github.com/LVD-AC)]
 - Alberto Cotronei [[@MagoKimbra](https://github.com/MagoKimbra)]
 - Marcio Teixeira [[@marcio-ao](https://github.com/marcio-ao)]
 - Chris Palmer [[@nophead](https://github.com/nophead)]
 - Chris Pepper [[@p3p](https://github.com/p3p)]
 - Steeve Spaggi [[@studiodyne](https://github.com/studiodyne)]
 - Thomas Moore [[@tcm0116](https://github.com/tcm0116)]
 - Teemu Mäntykallio [[@teemuatlut](https://github.com/teemuatlut)]
 - Nico Tonnhofer [[@Wurstnase](https://github.com/Wurstnase)]
 - [[@android444](https://github.com/android444)]
 - [[@bgort](https://github.com/bgort)]
 - [[@GMagician](https://github.com/GMagician)]
 - [[@Grogyan](https://github.com/Grogyan)]
 - [[@maverikou](https://github.com/maverikou)]
 - [[@oysteinkrog](https://github.com/oysteinkrog)]
 - [[@paclema](https://github.com/paclema)]
 - [[@paulusjacobus](https://github.com/paulusjacobus)]
 - [[@psavva](https://github.com/psavva)]
 - [[@Tannoo](https://github.com/Tannoo)]
 - [[@TheSFReader](https://github.com/TheSFReader)]
 - ...and many others

## License

Marlin is published under the [GPLv3 license](https://github.com/MarlinFirmware/Marlin/blob/1.0.x/COPYING.md) because we believe in open development. The GPL comes with both rights and obligations. Whether you use Marlin firmware as the driver for your open or closed-source product, you must keep Marlin open, and you must provide your compatible Marlin source code to end users upon request. The most straightforward way to comply with the Marlin license is to make a fork of Marlin on Github, perform your modifications, and direct users to your modified fork.

