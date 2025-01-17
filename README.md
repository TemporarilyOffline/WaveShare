### WaveShare GUI Mode:
***
# 64bit Boookworm
# Adapted From: https://www.waveshare.com/wiki/3.5inch_RPi_LCD_(B)_Manual_Configuration#For_Bookworm_System

```bash sudo apt install libraspberrypi-dev unzip cmake joe -y
wget https://files.waveshare.com/upload/1/1e/Waveshare35b-v2.zip
sudo unzip ./Waveshare35b-v2.zip -d /boot/overlays
wget https://files.waveshare.com/upload/1/1e/Rpi-fbcp.zip
unzip ./Rpi-fbcp.zip
cd rpi-fbcp/
mkdir build
cd build
cmake ..
make -j4
sudo install fbcp /usr/local/bin/fbcp

export EDITOR=joe
sudo $EDITOR /boot/firmware/config.txt
```
```bash
# comment out
# dtoverlay=vc4-kms-v3d
# max_framebuffers=2
```

Add the following to the end:

 ```bash
dtparam=spi=on
dtoverlay=waveshare35b-v2
hdmi_force_hotplug=1
max_usb_current=1
hdmi_group=2
hdmi_mode=1
hdmi_mode=87
hdmi_cvt 480 320 60 6 0 0 0
hdmi_drive=2
display_rotate=0
 ```

```$EDITOR ~/.bash_profile```
```bash
if [ "$(cat /proc/device-tree/model | cut -d ' ' -f 3)" = "5" ]; then
    # rpi 5B configuration
    export FRAMEBUFFER=/dev/fb1
    startx  2> /tmp/xorg_errors
else
    # Non-pi5 configuration
    export FRAMEBUFFER=/dev/fb0
    fbcp &
    startx  2> /tmp/xorg_errors
fi
```
```sudo $EDITOR /usr/share/X11/xorg.conf.d/99-fbturbo.~```
```bash
Section "Device"
        Identifier      "Allwinner A10/A13 FBDEV"
        Driver          "fbturbo"
        Option          "fbdev" "/dev/fb0"

        Option          "SwapbuffersWait" "true"
EndSection
```
```bash
sudo raspi-config nonint do_boot_behaviour B2
sudo raspi-config nonint do_wayland W1
```

# configure touch screen calibration
```bash
sudo apt-get install xserver-xorg-input-evdev xinput-calibrator -y
sudo cp /usr/share/X11/xorg.conf.d/10-evdev.conf /usr/share/X11/xorg.conf.d/45-evdev.conf
sudo $EDITOR /usr/share/X11/xorg.conf.d/99-calibration.conf
```
```bash
Section "InputClass"
        Identifier      "calibration"
        MatchProduct    "ADS7846 Touchscreen"
        Option  "Calibration"   "3932 300 294 3801"
        Option  "SwapAxes"      "1"
        Option "EmulateThirdButton" "1"
        Option "EmulateThirdButtonTimeout" "1000"
        Option "EmulateThirdButtonMoveThreshold" "300"
EndSection
```
***
# Add Keyboard

```sudo apt install matchbox-keyboard -y```

# create this file: 
```bash
export EDITOR=joe
$EDITOR ~/Desktop/inputmethods-matchbox-keyboard.desktop
```
```bash
[Desktop Entry]
Type=Link
Name=Keyboard
Icon=matchbox-keyboard
URL=/usr/share/applications/inputmethods/matchbox-keyboard.desktop
```
### WaveShare Text Mode:
***

# 64bit Boookworm
# Adapted From: https://www.waveshare.com/wiki/3.5inch_RPi_LCD_(B)_Manual_Configuration#For_Bookworm_System

```bash
sudo apt install libraspberrypi-dev unzip cmake joe -y
wget https://files.waveshare.com/upload/1/1e/Waveshare35b-v2.zip
sudo unzip ./Waveshare35b-v2.zip -d /boot/overlays
wget https://files.waveshare.com/upload/1/1e/Rpi-fbcp.zip
unzip ./Rpi-fbcp.zip
cd rpi-fbcp/
mkdir build
cd build
cmake ..
make -j4
sudo install fbcp /usr/local/bin/fbcp

export EDITOR=joe
sudo $EDITOR /boot/firmware/config.txt
```
```bash
# comment out
# dtoverlay=vc4-kms-v3d
# max_framebuffers=2
```

Add the following to the end:

 ```bash
dtparam=spi=on
dtoverlay=waveshare35b-v2
hdmi_force_hotplug=1
max_usb_current=1
hdmi_group=2
hdmi_mode=1
hdmi_mode=87
hdmi_cvt 480 320 60 6 0 0 0
hdmi_drive=2
display_rotate=0
 ```
```sudo $EDITOR /etc/rc.local```
```bash
#!/bin/bash
if [ "$(cat /proc/device-tree/model | cut -d ' ' -f 3)" = "5" ]; then
    # rpi 5B configuration
    export FRAMEBUFFER=/dev/fb1
    startx  2> /tmp/xorg_errors
else
    # Non-pi5 configuration
    export FRAMEBUFFER=/dev/fb0
    fbcp &
    startx  2> /tmp/xorg_errors
fi
exit 0
```

```bash
sudo chmod 755 /etc/rc.local
sudo raspi-config nonint do_boot_behaviour B2 # auto-login
```
