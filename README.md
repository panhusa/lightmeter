# lightmeter
A Lightmeter/Flashmeter for photographers, based on Arduino.

Components:
1. Arduino NANO v.3 https://www.banggood.com/custlink/K3Kvbdnnea
2. BH1750 light sensor https://www.banggood.com/custlink/mKDv2Ip1dr or https://www.banggood.com/custlink/GvGvnRNN0e
3. SSD1306 128*64 OLED SPI Display https://www.banggood.com/custlink/DDKmsdAQ6z
4. Buttons https://www.banggood.com/custlink/m3DGAYsnnY
5. 50x70 PCB https://www.banggood.com/custlink/KvvvnybQAP
6. AAA battery Holder https://www.banggood.com/custlink/vK3KsynANN

Thanks @morozgrafix https://github.com/morozgrafix for creating schematic diagram for this device.

The lightmeter based on Arduino as a main controller and BH1750 as a metering cell. Information is displayed on SSD1306 OLED display. The device is powered by 2 AAA batteries.

Functions list:

* Ambient light metering
* Flash light metering
* ND filter correction
* Aperture priority
* Shutter speed priority
* ISO range 8 - 4 000 000
* Aperture range 1.0 - 3251
* Shutter speed range 1/10000 sec - 133 min
* ND Filter range ND2 - ND8192
* Displaying amount of light in Lux.
* Displaying exposure value, EV
* Recalculating exposure pair while one of the parameter changing
* Battery information
* Power 2xAAA LR03 batteries

Detailed information on my site: https://www.pominchuk.com/lightmeter/

## Building

Open `src/lightmeter/lightmeter.ino` in the Arduino IDE (board: Arduino Nano, ATmega328P) and install these libraries from the Library Manager:

* BH1750 by Christopher Laws, **1.3.0 or newer** (older versions have a different `readLightLevel()` API)
* Adafruit SSD1306
* Adafruit GFX Library

Or with `arduino-cli`:

```
arduino-cli lib install "BH1750@1.3.0" "Adafruit SSD1306" "Adafruit GFX Library"
arduino-cli compile -b arduino:avr:nano src/lightmeter
```

The sensor is auto-ranged: if it saturates in ambient mode the reading is retaken at the lowest sensitivity. If it still saturates, `lx:OVER` is shown and the exposure shown is too long.

### Flash metering

Put the meter in flash mode (`F`), press the metering button, then fire the flash within 5 seconds. The meter adds up the light the flash adds above ambient across sensor readings, which gives the flash exposure. It then shows the aperture for the selected shutter speed, including ambient light during that time. Flash mode always works in shutter priority. Accuracy depends on the BH1750's integration time (16 ms typical), so check it against a known flash and adjust `FlashIntegrationTime` if needed.

### Power saving

After 60 seconds without a button press (`SleepTimeout`) the display turns off and the ATmega328 goes into power-down sleep. Any button wakes it, and the press that wakes it is ignored. The Nano's power LED and USB chip still draw current. For long battery life, remove the power LED or use a bare ATmega328/Pro Mini.
