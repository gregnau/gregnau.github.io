---
layout: post
title: 🚀 Read and emulate remotes with Raspberry Pi and LiRC
---

In the last few months i was thinking about how could i control a few things in my home. The solution was obvious at first: let's get a few relays (or dimmers), cables and start soldering. But then where to hide the cables and where to put the relays?! Most of the rooms have wallpaper and the housing of the light switches is small and already full with the cables and stuff. So this kept me thinking and then came to this idea. Nowadays many things come with remote controllers, IR or 433mhz. Why not begin the house automation with these devices?! It's an easy and relaxing task, not much of soldering skills is needed!
<!--more-->
I was using the following parts:
<ul>
	<li>TSOP4838 IR Sensor</li>
	<li>5mm IR LED 940nm</li>
	<li>2PN2222 NPN Transistor</li>
	<li>4,7K ohm resistor</li>
</ul>

<hr />

<strong>Part 1: Reading the remotes with LiRC</strong>

<a href="https://www.bianadventures.tk/wp-content/uploads/2015/06/raspi-fritzing2.jpg"><img class="alignnone size-medium wp-image-10" src="https://www.bianadventures.tk/wp-content/uploads/2015/06/raspi-fritzing2.jpg?w=300" alt="raspi-ir sensos" width="300" height="265" /></a><a href="https://www.bianadventures.tk/wp-content/uploads/2015/06/raspi-fritzing.jpg"><img class="alignnone size-medium wp-image-11" src="https://www.bianadventures.tk/wp-content/uploads/2015/06/raspi-fritzing.jpg?w=300" alt="raspi-fritzing" width="300" height="243" /></a>

The receiver should connected like on the image above and then follow the steps.

Install LiRC:

[code language="bash"]sudo apt-get install lirc[/code]

Paste the following line in /etc/modules:

[code]lirc_dev

lirc_rpi gpio_in_pin=18 gpio_out_pin=17[/code]

Uncomment or insert this line in /boot/config.txt:

[code]dtoverlay=lirc-rpi,gpio_in_pin=18,gpio_out_pin=17[/code]

Now edit the /etc/lirc/hardware.conf according to this:

[code]DRIVER=&quot;default&quot;

DEVICE=&quot;/dev/lirc0&quot;

MODULES=&quot;lirc_rpi&quot;[/code]

Reboot

After reboot check that the receiver is working properly,
<p style="padding-left:30px;">by stopping LiRC:</p>


[code language="bash"]
sudo /etc/init.d/lirc stop
[/code]

<p style="padding-left:30px;">then execute mode2:</p>


[code language="bash"]
mode2 -d /dev/lirc0
[/code]

Time to point a remote to the receiver and test some buttons.

If output is flooding the screen then it's most likely working good.

Start recording the remote(s):

[code language="bash"]irrecord -d /dev/lirc0 remote_name[/code]

<p style="padding-left:30px;">for the list of available keys:</p>


[code language="bash"]
irrecord --list-namespace
[/code]

That's it, now can copy the remote_name.conf to the /etc/lirc/lircd.conf:

[code language="bash"]sudo cp remote_name.conf /etc/lirc/lircd.conf[/code]

<p style="padding-left:30px;">then start LiRCd:</p>


[code language="bash"]
sudo /etc/init.d/lirc start
[/code]

<p style="padding-left:30px;">To try the recorded remote just run:</p>


[code language="bash"]
irw
[/code]

<p style="padding-left:30px;">It should output the key name when you press something on the remote.</p>


<hr />
<p style="padding-left:30px;">Part 2: Emulating remotes with LiRC</p>
<p style="padding-left:30px;"><a href="https://www.bianadventures.tk/wp-content/uploads/2015/06/untitled-sketch_bb1.jpg"><img class="alignnone size-medium wp-image-17" src="https://www.bianadventures.tk/wp-content/uploads/2015/06/untitled-sketch_bb1.jpg?w=300" alt="Untitled Sketch_bb1" width="300" height="242" /></a><a href="https://www.bianadventures.tk/wp-content/uploads/2015/06/untitled-sketch_schem2.jpg"><img class="alignnone wp-image-18" src="https://www.bianadventures.tk/wp-content/uploads/2015/06/untitled-sketch_schem2.jpg?w=150" alt="Untitled Sketch_schem2" width="290" height="220" /></a></p>
<p style="padding-left:30px;">Finally i ended up using 2 leds facing in 45 degree and like this it's covers the whole room.</p>
<p style="padding-left:30px;">To send a remote's signal:</p>


[code language="bash"]
irsend SEND_ONCE remote_name KEY_NAME
[/code]

<p style="padding-left:30px;">In Python is the best to call this command:</p>


[code language="python"]
os.system(&quot;irsend SEND_ONCE remote_name KEY_NAME&quot;)
[/code]

<p style="padding-left:30px;">With this simple solution i can now remotely control the led strips, the fireplace which serves as a electronic heater, the vacuum cleaner robot and the tv.</p>
