# Raspberry Pi & 3.5" TFT & Chromium as Kiosk

> Tested with *Raspbian GNU/Linux 10 (buster)*

## Turorials

* <https://avikdas.com/2018/12/31/setting-up-lcd-screen-on-raspberry-pi.html>

* <https://desertbot.io/blog/raspberry-pi-touchscreen-kiosk-setup>

* <https://www.raspberrypi.org/forums/viewtopic.php?t=238060>

## Setup

### 1. Prepare Raspberry Pi

1. Install Raspbian Lite
2. Update

```bash
  sudo apt-get update && sudo apt-get upgrade
```

3. Install Git

```bash
  sudo apt-get install -y git
```

### 2. Use privided Repository

1. Git Clone [GoodTFT/LCD-show](https://github.com/goodtft/LCD-show)

```bash
  git clone https://github.com/goodtft/LCD-show.git
  cd LCD-show
```

2. Copy boot overlays
  
```bash
  sudo cp ./usr/tft35a-overlay.dtb /boot/overlays/tft35a.dtbo
```

### 3. Configure Boot overlays

#### 1. Enable Interfaces

   Use `sudo raspi-config` and navigate to `5 Interfacing Options` and enable:

* P4: __SPI__
* P5: __I²C__ _(may not be needed)_
* P6: __Serial__ _(may not be needed)_

#### 2. Add the copied boot overly to the `config.txt`

* Open `/boot/config.txt` with a text editor (e.g. vi or nano) and add the following at the end:

```;
  #Adjust Rotation if needed
  dtoverlay=tft35a:rotate=270
```

* Adjust Overscan:

```;
  # uncomment this if your display has a black border of unused pixels visible
  # and your display can output without overscan
  disable_overscan=0

  # uncomment the following to adjust overscan. Use positive numbers if console
  # goes off screen, and negative if there is too much border
  #overscan_left=16
  #overscan_right=16
  #overscan_top=16
  #overscan_bottom=16
```

* Also add the following if needed:

```;
  #
  dtparam=spi=on
  dtparam=i2c_arm=on
  enable_uart=1
```
