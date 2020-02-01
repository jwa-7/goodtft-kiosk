# Raspberry Pi & 3.5" TFT & Chromium as Kiosk

> Tested with *Raspbian GNU/Linux 10 (buster)*

## Setup

### 1. Prepare Raspberry Pi

1. Install Raspbian Lite
    * Make sure *SSH* is enabled
      * Create an empty file named *ssh* in the `boot` partition

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

* Open `/boot/config.txt` with a text editor (e.g. vi or nano) and
add the following at the end:

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

### 4. Configure command line

* Open `/boot/cmdline.txt` with a text editor (e.g. vi or nano)
and add the following at the end of the line:

```;
  fbcon=map:10 fbcon=font:ProFont6x11
```

### 5. Reboot the Raspberry Pi

```;
  sudo reboot
```

### 6. Enable Interfaces

* Use `sudo raspi-config` and navigate to `3 Boot Options`

  * Go to `B1 Desktop / CLI`

    * Select `B2 Console Autologin`

* When asked to reboot, select **Yes**.

### 7. Install GUI components and Chromium

This may take some time!

#### 1. Minimun GUI components

```;
sudo apt-get install -y --no-install-recommends xserver-xorg x11-xserver-utils xinit openbox
```

#### 2. Chromium

```;
sudo apt-get install -y --no-install-recommends chromium-browser
```

### 8. Edit Openbox config

> *From [Raspberry Pi Touchscreen Kiosk Setup | desertbot.io](https://desertbot.io/blog/raspberry-pi-touchscreen-kiosk-setup)*:
>
> The Openbox window manager will be used to launch the Chromium browser.
>
> When Openbox launches at startup it will run two scripts in the `/etc/xdg/openbox` folder. The first, environment will setup any environment variables, etc. The second, autostart will setup and launch whatever app you specify.

#### 1. Config power management

* Open `/etc/xdg/openbox/autostart` with a text editor (e.g. vi or nano)
and add the following at the end of the file:

```;
  xset -dpms       # turn off display power management system
  xset s noblank   # turn off screen blanking
  xset s off       # turn off screen saver
```

#### 2. Supress error messages in case of chromium crashing

* Open `/etc/xdg/openbox/autostart` with a text editor (e.g. vi or nano)
and add the following at the end of the file:

```;
  # Remove exit errors from the config files that could trigger a warning
  sed -i 's/"exited_cleanly":false/"exited_cleanly":true/' ~/.config/chromium/'Local State'
  sed -i 's/"exited_cleanly":false/"exited_cleanly":true/; s/"exit_type":"[^"]\+"/"exit_type":"Normal"/' ~/.config/chromium/Default/Preferences
```

## X. References

* [Setting up an LCD screen on the Raspberry Pi, 2019 edition](https://avikdas.com/2018/12/31/setting-up-lcd-screen-on-raspberry-pi.html)

* [Raspberry Pi Touchscreen Kiosk Setup | desertbot.io](https://desertbot.io/blog/raspberry-pi-touchscreen-kiosk-setup)

* [GoodTFT 3.5" screen from Aliexpress - Raspberry Pi Forums](https://www.raspberrypi.org/forums/viewtopic.php?t=238060)

_*(Used in that order)*_
