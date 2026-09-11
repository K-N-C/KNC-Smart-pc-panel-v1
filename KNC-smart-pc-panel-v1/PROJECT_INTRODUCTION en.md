# KNC Smart PC Panel — Project Introduction

*This document is a translation of the original Turkish file (`PROJE_TANITIMI.md`). In case of any discrepancy, the original Turkish version takes precedence.*

## What Is This Project?

KNC Smart PC Panel is a hardware + software project that displays real-time
data such as your computer's **CPU/GPU temperature, usage rate, fan speed,
FPS** on a small **16x2 LCD screen** that can sit on your desk. It also
optionally allows you to **turn on your computer even while away from
home**.

It consists of two parts:

* **Windows program** (runs on your computer, reads and sends the data)
* **ESP32 + LCD screen** (small hardware that receives and displays this
data)

## How Does It Work? (General Logic)

```
[Your Computer]                                                                                [ESP32 + LCD]
     │                                                                                              │
  HWiNFO             ──►             Windows Program        ──►     USB         ──►            Displays on LCD
 (sensor data)          (Reads the data, sends it every second via USB cable)      (cycles through pages of CPU/GPU/RAM/...)
                                
```

1. A program called **HWiNFO** reads all the sensors in your computer
(temperature, usage, fan speed, etc.).
2. **KNC Smart PC Panel** (the Windows program) takes this data from HWiNFO
and sends it to the **ESP32** every second over a USB cable.
3. The **ESP32** cyclically displays the incoming data in pages (CPU, GPU,
RAM, VRAM, temperatures, fans, power, network speed, FPS...) on the
**16x2 LCD screen**.
4. A **DS18B20 temperature sensor** is also connected to the ESP32 — this
directly measures the case interior temperature, shows it on the screen
(and reports it back to the PC program).
5. An optional **relay module** is connected to the motherboard's power
button pins. Through a cloud service called **SinRic Pro**, you can send
a command from your phone to turn on your computer (even while away
from home).

## What Is ESP32, and What Does It Do in This Project?

The ESP32 is a small, cheap, programmable microcontroller (mini computer)
board that also has **Wi-Fi**. It works on the same principle as Arduino,
but having Wi-Fi makes it ideal for this project. In this project, the
ESP32's tasks are:

* Reading the data coming from the PC over USB and displaying it on the LCD
* Measuring the case interior temperature from the DS18B20 temperature
sensor
* Connecting to Wi-Fi and communicating with the SinRic Pro cloud service
* When a remote "turn on" command arrives, triggering a relay to act as
if it "pressed" the motherboard's power button

## Purpose of the Project

Monitoring temperatures in real time on gaming computers or powerful
systems usually requires looking at the screen (opening an overlay). This
project keeps this information visible at all times on a **separate, small
physical screen**, providing a neat look while also making it easier to
manage your computer even when it's not within reach (thanks to the remote
power-on feature). It is a free hobby project for personal and
non-commercial use.

## Materials Used (Hardware)

|Material|Description|
|-|-|
|ESP32 development board|Any Wi-Fi-enabled ESP32 DevKit|
|16x2 I2C LCD screen|Must have an I2C adapter (4-pin: VCC/GND/SDA/SCL)|
|Relay module (optional)|5V single channel, for the remote power-on feature|
|DS18B20 temperature sensor|For measuring case interior temperature, digital (1-Wire)|
|4.7kΩ resistor|For the DS18B20's data line (pull-up resistor)|
|USB cable|To connect the ESP32 to the PC (both power and data)|
|Jumper wires|For connections|
|Box/case (optional)|For a tidy appearance|

## Hardware Connections

|LCD I2C (SDA)|GPIO 21|Your board's default I2C pin (if unchanged)|
|LCD I2C (SCL)|GPIO 22|Your board's default I2C pin (if unchanged)|
|Relay IN (signal)|**GPIO 26**|Active-HIGH relay assumed|
|Relay VCC / GND|5V (or 3.3V) / GND|Depending on your relay module|
|Relay NO+COM|In **parallel** with the motherboard's **Power SW (PWR\_SW)** pins|Same place as the case power button|
|DS18B20 DATA|**GPIO 4**|A 4.7kΩ pull-up resistor between DATA and VCC is required|
|DS18B20 VCC / GND|3.3V / GND||

The circuit diagram is attached.

The relay is triggered for **exactly 1 second** and then turns off
automatically (simulating a short press of the physical power button) — it
is not held long enough to force-shutdown the motherboard.

## Required Software

**To run on the computer:**

* **HWiNFO** — for reading sensor data.
During installation, the "Shared Memory Support" option must be checked.
* **KNC Smart PC Panel** — the Windows program for this project (available
as a ready-made `.exe`, or can be compiled from source with Visual
Studio).

**To program the ESP32 (done once):**

* **Arduino IDE** (free)
* Adding **ESP32 board support** to Arduino IDE (via Board Manager)
* The following libraries (installed from the Arduino IDE's Library
Manager): `LiquidCrystal I2C`, `OneWire`, `DallasTemperature`, `SinricPro`

**For those who want to use the remote power-on feature (optional):**

* An account on **SinRic Pro** and a "Switch" device

## Setup From Scratch (For Someone Who Knows Nothing)

1. **Assemble the hardware:** Connect the LCD screen to the ESP32's I2C
pins, the DS18B20 (if present) to the specified pin, and the relay (if
present) to the specified pin. The full pin list is at the beginning of
the `.ino` file.
2. **Install the Arduino IDE**, add ESP32 board support and the 4 libraries
above.
3. Plug the ESP32 into the computer via USB, open the `.ino` file in the
Arduino IDE, select the correct board/port, and press the **Upload**
button.
4. **Download and install HWiNFO**, check Settings → Shared Memory Support,
and leave HWiNFO running.
5. Run the **KNC Smart PC Panel** program. On first launch, you'll be
greeted by a **Setup Wizard**; simply follow the steps in order: language
selection, terms of use confirmation, HWiNFO check, selecting the
correct COM port, matching the sensors, and (optionally) entering your
Wi-Fi/SinRic Pro information.
6. Once setup is complete, your real data will start appearing on the
screen within a few seconds. 🎉

If you run into a problem, while the program is running in the background
(it keeps running in the system tray even if you close it with the X
button), you can go back and edit the settings at any time from the
**Settings** and **Setup Wizard** options.

## Key Features

* Real-time CPU/GPU/RAM/VRAM temperature and usage tracking
* Direct measurement of case interior temperature via the ESP32 (DS18B20)
* Adjustable temperature warning thresholds (blinking warning on screen)
* Turning on your computer remotely from home with SinRic Pro
* Turkish / English language support
* Auto-start on Windows boot, runs in the system tray
* Search/filter support when matching sensors

## LCD Screen Pages

Page 1
CPU 53.4°   %18
GPU 49.6°   %14

Page 2
RAM   55.6°    %46
VRAM  50.5°    %64

Page 3
BOARD 40.5°
CASE 30.2°

Page 4
SSD1  42°
SSD2  40°

Page 5
NET ↓  12 MB
NET ↑  1.5 MB

Page 6
CPU  1450 rpm
GPU  1500 rpm

Page 7
CPU 65W
GPU 180W

Page 8
FPS 150
WiFi OK USB OK

Warning screen
⚠ CPU HOT ! ⚠
  87.2°C

PC off
KNCSmartPCPanel
WiFi OK  Pc OFF

When the computer is starting up
KNC Smart PC

Starting...
↓
Loading...
↓
Normal pages.

*This project is shared free of charge for personal and non-commercial use.
See the `LICENSE.md` file for terms of use and disclaimer.*
