---
layout: post
title: 📺 Waveshare Spotpear 3.2“ (v4) TFT-LCD with touch input + 3 buttons
---

Cheap chinese tft lcd module with touch and 3 physical buttons. Connect the lcd on the Raspberry Pi and boot up the Raspbian/Debian (min. Wheezy), then follow the steps below to get it fully functional and calibrated:

<!--more-->

Set the default FRAMEBUFFER to <em>/dev/fb1</em> (accelerated output)
<pre class="prettyprint">sudo nano /usr/share/X11/xorg.conf.d/99-fbturbo.conf</pre>
and change <em>Option “fbdev” “/dev/fb0″</em> to <em>fb1</em>.

&nbsp;

Download the driver for the LCD, then reboot
<pre class="prettyprint">sudo REPO_URI=https://github.com/notro/rpi-firmware rpi-update
sudo reboot</pre>
Enable SPI via <em>raspi-config</em>
<!--?prettify linenums=true?-->
<pre class="prettyprint">sudo raspi-config</pre>
find the “<em>Advanced Options</em>” line, then enter yes and reboot.

&nbsp;

Add modules to load at boot
<pre class="prettyprint">sudo nano /etc/modules</pre>
add the code below the <em>snd-bcm2835</em> line:
<pre class="prettyprint">spi-bcm2708
ads7846
fbtft_device rotate=270 name=waveshare32b gpios=dc:22,reset:27 speed=48000000
fb_ili9340 width=320 height=240 buswidth=8 init=-1,0xCB,0x39,0x2C,0x00,0x34,0x02,-1,0xCF,0x00,0XC1,0X30,-1,0xE8,0x85,0x00,0x78,-1,0xEA,0x00,0x00,-1,0xED,0x64,0x03,0X12,0X81,-1,0xF7,0x20,-1,0xC0,0x23,-1,0xC1,0x10,-1,0xC5,0x3e,0x28,-1,0xC7,0x86,-1,0x36,0x28,-1,0x3A,0x55,-1,0xB1,0x00,0x18,-1,0xB6,0x08,0x82,0x27,-1,0xF2,0x00,-1,0x26,0x01,-1,0xE0,0x0F,0x31,0x2B,0x0C,0x0E,0x08,0x4E,0xF1,0x37,0x07,0x10,0x03,0x0E,0x09,0x00,-1,0XE1,0x00,0x0E,0x14,0x03,0x11,0x07,0x31,0xC1,0x48,0x08,0x0F,0x0C,0x31,0x36,0x0F,-1,0x11,-2,120,-1,0x29,-1,0x2c,-3</pre>
*** all in one line!

&nbsp;

Add the code below to <em>/boot/cmdline.txt</em>:
<pre class="prettyprint">sudo nano /boot/cmdline.txt</pre>
<pre class="prettyprint">dwc_otg.lpm_enable=0 console=ttyAMA0,115200 console=tty1 root=/dev/mmcblk0p6 rootfstype=ext4 elevator=deadline rootwait fbtft_device.custom fbtft_device.name=waveshare32b fbtft_device.gpios=dc:22,reset:27 fbtft_device.bgr=1 fbtft_device.speed=48000000 fbcon=map:10 fbcon=font:ProFont6x11 logo.nologo dma.dmachans=0x7f35 console=tty1 consoleblank=0 fbtft_device.fps=50 fbtft_device.rotate=0</pre>
*** all in one line!

&nbsp;

Reboot again
<pre class="prettyprint">sudo reboot</pre>
&nbsp;

Activating the touchscreen
<pre class="prettyprint">sudo nano /boot/config.txt</pre>
Insert this line on the end of config.txt:
<pre class="prettyprint">dtoverlay=ads7846,speed=500000,penirq=17,swapxy=1</pre>
&nbsp;

Calibation Entry
<pre class="prettyprint">sudo nano /usr/share/X11/xorg.conf.d/99-calibration.conf
<!--?prettify linenums=true?--></pre>
<pre class="prettyprint">Section "InputClass"
    Identifier "calibration"
    MatchProduct "ADS7846 Touchscreen"
    Option "Calibration" "3900 240 3900 240"
    Option "InvertX" "1"
EndSection</pre>
Invert the axis if you need to (1 1 = x y) by running the command below:
<!--?prettify linenums=true?-->
<pre class="prettyprint">DISPLAY=:0 xinput --set-prop 'ADS7846 Touchscreen' 'Evdev Axis Inversion' 1 1</pre>
