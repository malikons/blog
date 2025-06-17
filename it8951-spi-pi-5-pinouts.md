# Waveshare e-Ink Display HAT for Raspberry Pi - Pinouts for SPI to 40-Pin GPIO connection

Looking at that Waveshare page, https://www.waveshare.com/wiki/7.8inch_e-Paper_HAT - we need to figure out the wiring for the PH2.0 connector. Easier way to connect the HAT with GPIO is to plug it directly on top of the Pi GPIO connector and you're done. However, in my case, I don't want this HAT to be on top of Pi 5 because of the fan and ventilation concerns and also to keep my custom Pi 5 case slim. Hence I decided to use the provided PH2.0 8-Pin connector that comes with Waveshare e-Ink display.

SPI pinout table is already in the documentation, linked above. Based on that information, here's how to wire your **PH2.0 20cm 8-Pin** connector to connect the e-ink HAT to your Pi 5:

**SPI Wiring for PH2.0 Connector:**

| Wire # | IT8951 HAT Pin | Pi 5 GPIO (BCM) | Pi 5 Physical Pin | Function |
|--------|----------------|-----------------|-------------------|----------|
| 1 | 5V | 5V | Pin 2 or 4 | Power |
| 2 | GND | GND | Pin 6, 9, 14, 20, 25, 30, 34, or 39 | Ground |
| 3 | MISO | GPIO 9 | Pin 21 | SPI MISO |
| 4 | MOSI | GPIO 10 | Pin 19 | SPI MOSI |
| 5 | SCK | GPIO 11 | Pin 23 | SPI Clock |
| 6 | CS | GPIO 8 | Pin 24 | SPI Chip Select |
| 7 | RST | GPIO 17 | Pin 11 | Reset (active low) |
| 8 | HRDY | GPIO 24 | Pin 18 | Busy/Ready status |

## Important notes:
- Make sure the **DIP switch on the HAT is set to SPI mode** (not I80)
- The HAT expects **5V power**, so use the 5V pins on the Pi
- **Don't hot-plug** - connect all cables before powering on
- The documentation mentions you need to enable SPI interface with `sudo raspi-config`

The 8-pin PH2.0 connector should have enough wires for all the required connections. Just make sure you check which end of your cable corresponds to which wire position when connecting to both the HAT and the Pi GPIO pins.


Moreover, here's how to identify pin 1 on your Pi 5 GPIO:

## Physical Location:
- Pin 1 is at the **corner closest to the SD card slot**
- It's the **first pin on the left side** when looking at the Pi with the GPIO pins at the top
- The Pi 5 has a **small "1" marking** printed on the PCB next to pin 1

A nice diagram I found on Raspberry Pi 5 official documentation:

![GPIO-Pinout-Diagram-2](https://github.com/user-attachments/assets/ec9d1d8a-073f-423f-9fdc-5132becc3f76)

Reference to Pi Documentation: https://www.raspberrypi.com/documentation/computers/raspberry-pi.html#gpio

## Visual Reference:
```
SD Card Slot
    ↓
[1] [2]  ← Pin 1 is here (3.3V)
[3] [4]  ← Pin 2 is here (5V)
[5] [6]
[7] [8]
...
[39][40]
```

**Easy way to remember:**
- Pin 1 = **3.3V** (you can verify with a multimeter)
- Pin 2 = **5V** (next to pin 1)
- Pins are numbered **odd on the left, even on the right**
- Pin 1 is always the **top-left** when GPIO is oriented at the top

**Additional verification:**
- Pin 1 should have **3.3V** when the Pi is powered on
- Pin 2 (right next to it) should have **5V**
- You can use a multimeter to confirm these voltages

**For the waveshare e-ink connection:**
- You'll want **Pin 2 or 4 for 5V** (not pin 1, which is 3.3V)
- Pin 6 is a convenient **GND** right below the 5V pins

The Pi 5 should also have the pin numbers printed on the PCB if you look closely.
