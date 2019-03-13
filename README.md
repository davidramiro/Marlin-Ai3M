# Anycubic i3 Mega Marlin 1.1.9 by davidramiro

[![Donate](https://img.shields.io/badge/Donate-PayPal-green.svg)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=3MFT8QMP5ZRCE&source=url) [![Downloads](https://img.shields.io/github/downloads/davidramiro/Marlin-AI3M/total.svg?style=flat)](https://github.com/davidramiro/Marlin-AI3M/releases) [![Open Issues](https://img.shields.io/github/issues-raw/davidramiro/Marlin-AI3M.svg?style=flat)](https://github.com/davidramiro/Marlin-AI3M/issues?q=is%3Aopen+is%3Aissue) [![Closed Issues](https://img.shields.io/github/issues-closed-raw/davidramiro/Marlin-AI3M.svg?style=flat)](https://github.com/davidramiro/Marlin-AI3M/issues?q=is%3Aissue+is%3Aclosed) [![License](https://img.shields.io/github/license/davidramiro/Marlin-AI3M.svg?style=flat)](https://github.com/davidramiro/Marlin-AI3M/blob/master/LICENSE) [![Latest Release](https://img.shields.io/github/release/davidramiro/Marlin-AI3m.svg?style=flat)](https://github.com/davidramiro/Marlin-AI3M/releases/latest/) ![](https://img.shields.io/github/last-commit/davidramiro/Marlin-AI3m.svg?style=flat)

This is my slightly customized version of the [Marlin Firmware](https://github.com/MarlinFirmware/Marlin), gratefully based on [derhopp's repo](https://github.com/derhopp/Marlin-with-Anycubic-i3-Mega-TFT) with his remarkable efforts to get the Anycubic i3 Mega TFT screen to work.

Feel free to discuss issues and work with me further optimizing this firmware!

This firmware works on every i3 Mega that has two Z-axis endstops. If you have an older model with only one Z endstop, a small [adjustment](https://github.com/davidramiro/Marlin-AI3M/wiki/Customization-&-Compiling#single-z-endstop) is required.

The new **Mega-S** should work too, but you will need to enter two additional commands after flashing the firmware, mentioned in the instructions below.

Looking for a **BLtouch firmware**? Head [this way](https://github.com/MNieddu91/Marlin-AI3M-BLTouch)! Mounting and configuration instructions are included.

Note: This is just a firmware, not magic. A big part of print quality still depends on your slicer settings and mechanical condition of your machine.

#### Make sure to take a look at the [Wiki](https://github.com/davidramiro/Marlin-AI3M/wiki/), especially the [FAQ](https://github.com/davidramiro/Marlin-AI3M/wiki/Frequently-Asked-Questions).

A German translation of the instructions can be found [here](https://kore.cc/i3mega/download/marlin-ai3m_german.pdf).

## Why use this?

While the i3 Mega is a great printer for its price and produces fantastic results in stock, there are some issues that are easily addressed:

- Many people have issues getting the Ultrabase leveled perfectly, using Manual Mesh Bed Leveling the printer generates a mesh of the flatness of the bed and compensates for it on the Z-axis for perfect prints without having to level with the screws.
- Much more efficient bed heating by using PID control. This uses less power and holds the temperature at a steady level. Highly recommended for printing ABS.
- Fairly loud fans, while almost every one of them is easily replaced, the stock FW only gives out 9V instead of 12V on the parts cooling fan so some fans like Noctua don't run like they should. This is fixed in this firmware.
- Even better print quality by adding Linear Advance, S-Curve Acceleration and some tweaks on jerk and acceleration.
- Thermal runaway protection: Reducing fire risk by detecting a faulty or misaligned thermistor.
- Very loud stock stepper motor drivers, easily replaced by Watterott or FYSETC TMC2208. To do that, you'd usually have to flip the connectors on the board, this is not necessary using this firmware.
- No need to slice and upload custom bed leveling tests, test it with a single GCode command
- Easily start an auto PID tune or mesh bed leveling via the special menu (insert SD card, select special menu and press the round arrow)
- Filament change feature enabled: Switch colors/material mid print (instructions below) and control it via display.
- The filament runout, pause and stop functionality have been overhauled and improved: The hotend now parks and retracts (on pause or stop) and purges automatically (on resume).
- Added `M888` cooldown routine for the Anycubic Ultrabase (EXPERIMENTAL): This is meant to be placed at the end Gcode of your slicer. It hovers over the print bed and does circular movements while running the fan. Works best with custom fan ducts.
  - Optional parameters:
  - `T<temperature>`:   Target bed temperature (min 15°C), 30°C if not specified (do not set this under room temperature)
  - `S<fan speed>`:   Fan speed between 0 and 255, full speed if not specified
  - e.g. `M888 S191 T25`: run the fan at 75% until the bed has cooled down to 25°C


## Known issues:

- Power outage support is not included
- Estimated print times from your slicer might be slightly off.
- Special characters on any file or folders name on the SD card will cause the file menu to freeze. Simply replace or remove every special character (Chinese, Arabic, Russian, accents, German & Scandinavian umlauts, ...) from the name. Symbols like dashes or underscores are no problem.
**Important note: On the SD card that comes with the printer there is a folder with Chinese characters in it by default. Please rename or remove it.**

## How to flash this?

I provided three different precompiled hex files: One for no modifications on the stepper motor drivers - good for people who didn't touch anything yet, one for boards with TMC2208 installed and where the connectors have been flipped and one with TMC2208 and the connectors in original orientation.

### Choose your precompiled hex:

- Download the precompiled firmware here: [Releases](https://github.com/davidramiro/Marlin-AI3M/releases)
- Choose the correct hex file:
- For TMC2208 with connectors in original orientation, use `Marlin-AI3M-vx.x.x-TMC2208.hex`
- If you use TMC2208 and already reversed your connectors, use `Marlin-AI3M-vx.x.x-TMC2208_reversed.hex`
- If you use a newer version of the TMC2208 that doesn't require the connector to be reversed (TMC2208 "v2.0" written on the PCB, chip on the top side), please also use `Marlin-AI3M-vx.x.x-TMC2208_reversed.hex`.
- If you use the original stepper motor drivers, use `Marlin-AI3M-vx.x.x-stock_drivers.hex`.

### Or compile it yourself:

- Download and install [Arduino IDE](https://www.arduino.cc/en/main/software)
- Clone or download this repo
- Browse into the Marlin folder and run `Marlin.ino`
- In the IDE, under `Tools -> Board` select `Genuino Mega 2560` and `ATmega2560`
- Open Marlin.ino in the Marlin directory of this repo
- [Customize if needed](https://github.com/davidramiro/Marlin-AI3M/wiki/Customization-&-Compiling) (e.g. motor directions and type at line `559` to `566` and line `857` to `865` in `Configuration.h`)
- Under `Sketch`, select `Export compiled binary`
- Look for the .hex file in the Marlin directory (only use the `Marlin.ino.hex`, not the `Marlin.ino.with_bootloader.hex`!)

### After obtaining the hex file:

- Flash the hex with Cura, OctoPrint or similar
- Use a tool with a terminal (OctoPrint, Pronterface, Repetier Host, ...) to send commands to your printer.
- **Important** Connect to the printer and send the following commands:
- `M502` - load hard coded default values
- `M500` - save them to EEPROM

**If you are using this on a Mega-S, those two additional commands are necessary:**
- `M92 E384` - set correct steps for the new extruder
- `M500` - save them
- I highly recommend calibrating the extruder.

#### Calibration and other instructions have been moved to the [Wiki](https://github.com/davidramiro/Marlin-AI3M/wiki/Calibration).

## Manual Mesh Bed Leveling

If you have issues with an uneven bed, this is a great feature.

- Insert an SD card, enter the print menu.
- Enter the special menu by selecting it and pressing the round arrow:

![Special Menu][menu]

- In this menu, the round arrow is used to execute the command you selected.
- Preheat the bed to 60°C with this entry: (if you usually print with a hotter bed, use the Anycubic menu)

![Preheat bed][preheat]

- Level your preheated bed as well as you can with the four screws.
- Start the mesh leveling:

![Start MMBL][start]

- Your nozzle will now move to the first calibration position.
- Don't adjust the bed itself with screws, only use software from here on!
- Use a paper - I recommend using thermopaper like a receipt or baking paper
- Use the onscreen controls to lower or raise your nozzle until you feel a light resistance: (**If you want to send the same command multiple times, select the item again, even though it is still marked red.**)

![Z axis controls][control]

- Once finished , move to the next point:

![Next mesh point][next]

- Repeat the last two steps until all 25 points are done.
- Your printer will beep, wait 20 seconds and then save (the printer will beep once more to confirm):


![Save to EEPROM][save]

- Reboot your printer.


[menu]: https://kore.cc/i3mega/mmbl/menu.jpg "Special Menu"
[preheat]: https://kore.cc/i3mega/mmbl/preheat.jpg "Preheat 60C"
[start]: https://kore.cc/i3mega/mmbl/start.jpg "Start Mesh leveling"
[next]: https://kore.cc/i3mega/mmbl/next.jpg "Next Mesh point"
[control]: https://kore.cc/i3mega/mmbl/control.jpg "Z axis control"
[save]: https://kore.cc/i3mega/mmbl/save.jpg "Save to EEPROM"

### After leveling:

- To ensure your mesh gets used on every print from now on, go into your slicer settings and look for the start GCode
- Look for the Z-homing (either just `G28` or `G28 Z0`) command and insert these two right underneath it:
```
M501
M420 S1
```
- Your printer should now correctly print first layers even on a warped bed.
- When working on the printer, installing a new hotend or nozzle or the bed warping over time, a new Mesh Leveling procedure is recommended.

#### Manual commands for use with OctoPrint etc.:

- `G29 S1` - Start MMBL
- `G29 S2` - Next Mesh point
- Raising Z: `G91`, `G1 Z0.02`, `G90` (one after another, not in one line)
- Lowering Z: `G91`, `G1 Z-0.02`, `G90`
- After seeing `ok` in the console, send `M500` to save.


### Testing your bed leveling

- No need to download or create a bed leveling test, simply send those commands to your printer:
```
G28
G26 C H200 P25 R25
```
- To adjust your filament's needed temperature, change the number of the `H` parameter
- Default bed temperature is 60°C, if you need another temperature, add e.g. `B80`
- If your leveling is good, you will have a complete pattern of your mesh on your bed that you can peel off in one piece
- Don't worry if the test looks a bit messy, the important thing is just that the line width is the same all over the mesh
- Optional: Hang it up on a wall to display it as a trophy of how great your leveling skills are.


## M600 Filament Change

![M600 Demo][m600 demo]

[m600 demo]: https://kore.cc/i3mega/img/m600demo.jpg "M600 demo"

**BETA: This now also works without USB printing, via SD & display.**

#### Configuration (only needed once):
- Send `M603 L0 U0` to use manual loading & unloading.
- Send `M603 L538 U555` to use automatic loading & unloading
  - The `L` and `U` paramters define the load and unload length in mm. The values above work well on a stock setup, if you modded your extruder, bowden tube or hotend, you might need to adjust those.
- Save with `M500`

#### Filament change process (manual loading):
- For printing via SD:
  - Place `M600` in your GCode at the desired layer
- For printing via USB:
  - Place `M600` in your GCode at the desired layer or send it via terminal
  - Alternatively: Use `FilamentChange Pause` in the Special Menu
- The nozzle will park and your printer will beep
  - For safety reasons, the printer will turn off the hotend after 10 minutes. If you see the temperature being under the target:
    - SD printing: Click `CONTINUE` **(only once!)** on the screen and wait for the hotend to heat up again.
    - USB printing: Send `M108` and wait for the hotend to heat up again.
- Remove the filament from the bowden tube
- Insert the new filament right up to the nozzle, just until a bit of plastic oozes out
- Remove the excess filament from the nozzle with tweezers
- For printing via SD:
  - Click `CONTINUE` on the screen
- For printing via USB:
  - Send `M108` via your USB host or use `FilamentChange Resume` in the Special Menu
  - Note for OctoPrint users: After sending `M108`, enable the advanced options at the bottom of the terminal and press `Fake Acknowledgement`

#### Filament change process (automatic loading):
- For printing via SD:
  - Place `M600` in your GCode at the desired layer
- For printing via USB:
  - Place `M600` in your GCode at the desired layer or send it via terminal
  - Alternatively: Use `FilamentChange Pause` in the Special Menu
- The nozzle will park
- The printer will remove the filament right up to the extruder and beep when finished
  - For safety reasons, the printer will turn off the hotend after 10 minutes. If you see the temperature being under the target:
    - SD printing: Click `CONTINUE` **(only once!)** on the screen and wait for the hotend to heat up again.
    - USB printing: Send `M108` and wait for the hotend to heat up again.
- Insert the new filament just up to the end of the bowden fitting, as shown here:

![Load Filament][m600 load]

[m600 load]: https://kore.cc/i3mega/img/load.jpg "M600 Load"

- For printing via SD:
  - Click `CONTINUE` on the screen
- For printing via USB:
  - Send `M108` via your USB host or use `FilamentChange Resume` in the Special Menu
  - Note for OctoPrint users: After sending `M108`, enable the advanced options at the bottom of the terminal and press `Fake Acknowledgement`
- The printer will now pull in the new filament, watch out since it might ooze quite a bit from the nozzle
- Remove the excess filament from the nozzle with tweezers


## Updating

### Back up & restore your settings

Some updates require the storage to be cleared (`M502`), if mentioned in the update log. In those cases, before updating, send `M503` and make a backup of all the lines starting with:

```
M92
G29
M301
M304
```

After flashing the new version, issue a `M502` and `M500`. After that, enter every line you saved before and finish by saving with `M500`.

## Something went wrong?
No worries. You can easily go back to the default firmware and restore the default settings.
- Flash the hex file from the [manufacturer's website](http://www.anycubic3d.com/support/show/594016.html) (in case it's offline, I have uploaded the stock firmwares [here](https://kore.cc/i3mega/download/stockFW/) as well).
- After flashing, send `M502` and `M500`. Now your machine is exactly as it came out of the box.


## Detailed changes:

- Thermal runaway protection thresholds tweaked
- Manual mesh bed leveling enabled ([check this link](https://github.com/MarlinFirmware/Marlin/wiki/Manual-Mesh-Bed-Leveling) to learn more about it)
- Heatbed PID mode enabled
- TMC2208 configured in standalone mode
- Stepper orientation flipped (you don't have to flip the connectors on the board anymore)
- Linear advance unlocked (Off by default. [Research, calibrate](http://marlinfw.org/docs/features/lin_advance.html) and then enable with `M900 Kx`)
- S-Curve Acceleration enabled
- G26 Mesh Validation enabled
- Some redundant code removed to save memory
- Minor tweaks on default jerk and acceleration
- Print statistics enabled (send `M78` to read them)
- `M600` filament change feature enabled
  - Implemented easy resume via display
- Filament runout behaviour tweaked
  - Added purge and retract
  - Move nozzle to park position on runout
  - Prevent false positives by adding a small delay to the sensor
- Pause and stop behaviour tweaked
- Added `M888` cooldown routine for the Anycubic Ultrabase


## Changes by [derhopp](https://github.com/derhopp/):

- 12V capability on FAN0 (parts cooling fan) enabled
- Buzzer disabled (e.g. startup beep)
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

## Disclaimer

```
/*
* Flashing a custom firmware happens at your own risk.
*
* THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS
* AND CONTRIBUTORS “AS IS” AND ANY EXPRESS OR IMPLIED
* WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED
* WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A
* PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL
* THE COPYRIGHT OWNER OR CONTRIBUTORS BE LIABLE FOR
* ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR
* CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO,
* PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS
* OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION)
* HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER
* IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE
* OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS
* SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
*/
```
