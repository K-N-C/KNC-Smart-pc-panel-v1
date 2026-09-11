# KNC Smart PC Panel

*This document is a translation of the original Turkish file (`README.md`). In case of any discrepancy, the original Turkish version takes precedence.*

A utility software that transfers temperature, usage, and performance data
from your computer to a 16x2 LCD screen connected to an ESP32, and also
lets you turn on your computer via SinRic Pro even while away from home.

## Features

* Reads CPU/GPU/RAM/VRAM/SSD temperature and usage data from HWiNFO
* Displays it in pages on an ESP32 + 16x2 I2C LCD screen
* Measures case interior temperature directly via the ESP32 using DS18B20
* Configurable temperature warning thresholds (blinking warning on screen)
* Remote PC power-on via SinRic Pro (Power SW simulation using a relay)
* Runs in the background in the system tray, auto-starts on Windows boot
* Step-by-step setup wizard on first run

## Installation

### Windows Program

Download and run the exe file; a setup screen will greet you — fill in the
required information on the setup screen and continue.

### ESP32 Firmware

Open the `KNCSmartPCPanel\\\_ESP32.ino` file with the Arduino IDE, install
the following libraries, and then upload it to the ESP32:

* LiquidCrystal I2C
* OneWire
* DallasTemperature
* SinricPro

For hardware connections and the pin diagram, see the comment block at the
beginning of the `.ino` file.

## Requirements

* Windows 10/11
* [HWiNFO](https://www.hwinfo.com/) (with Shared Memory Support enabled)
* ESP32 development board + 16x2 I2C LCD screen
* (Optional) Relay module + DS18B20 temperature sensor

## Notes

* Closing the window (X) does not close the program; it minimizes it to
the system tray. To close it completely, right-click the tray icon →
**Exit**.
* The case temperature is read from the DS18B20, not from the PC; if the
sensor is not connected or the connection is lost, the screen will show
**"CASE SENSOR YOK"** ("NO CASE SENSOR").
* When you change the temperature thresholds from Settings →
**Temperature Thresholds** tab and press Save, they are sent to the
ESP32 instantly (no restart required).
* Every time the SinRic Pro information is saved, the ESP32 restarts
itself (to establish a clean connection) — the screen may stay blank
for a few seconds.

## License

See [LICENSE.md](LICENSE.md). It is free for personal and non-commercial
use. HWiNFO, SinRic Pro, and other third-party product/service names
belong to their respective rights holders; this project is not an
official part of them.
