---
layout: post
title: 📺 Raspberry Pi mediacenter on the cheap
---

<a href="https://www.bianadventures.tk/wp-content/uploads/2015/07/maxresdefault.jpg"><img class=" size-full wp-image-120 aligncenter" src="https://www.bianadventures.tk/wp-content/uploads/2015/07/maxresdefault.jpg" alt="maxresdefault" width="360" height="221" /></a> The bedroom left without screen and it's something i can't live without. Since i find television programs annoying and full-HD flatscreen tv's are still above € 150-200, the Raspberry Pi hooked on a HDMI monitor was an obvious choice. After some looking online i even found a used one - but good as new -, about 1 year old with still a year of warranty and also in driving distance of 1 hour. This cost me with petrol included € 110 and it turned out the guy had also a Raspberry Pi Model B rev2.0 for sale. I bought it also for a reasonable € 15, which means a complete 30" mediacenter (smart tv?) for € 125. At the time it is a really nice price i think. Although there is a huge con on the monitors: the vertical viewing angle is very narrow, so if you watch the screen from above or below then it's going to fade. I never knew this before i faced with the issue. Watch out on this when you placing, i needed to lower the mount after installation and thus repaint the wall to cover the first holes. I didn't noticed similar on tv's, those have pretty good viewing angles nowadays.

<!--more-->

I went with the OSMC - Open Source Media Center which i prefer over the Openelec, based on personal experience. Both of them are great choice, though this article covers only OSMC for now.

First get the OSMC image:

[code language="bash"]wget http://download.osmc.tv/installers/diskimages/OSMC_TGT_rbp1_20150628.img.gz[/code]

Then extract the image file on the sd card:

[code language="bash"]tar zxvf OSMC_TGT_rbp1_20150628.img.gz[/code]

Now you need to write the .img file on the sd-card: <em>*** do not forget to modify the path /dev/sdc to your sd-card!!! ***</em>

[code language="bash"]pv &amp;lt; OSMC_TGT_rbp1_20150628.img.gz &amp;gt; /dev/sdc[/code]

...also it needs to run as <em>root</em>, but check your path again ;) It's time to put the sd-card in the Raspberry Pi but don't boot yet if you have no keyboard or <a href="https://raspbianadventures.wordpress.com/2015/06/26/read-and-emulate-remotes-with-raspberry-pi-and-lirc/" target="_blank">ir-receiver</a> attached. To use a remote control grab the receiver circuit from the last link.

After boot it should start extracting/installing the system and let you setup after (wifi also).

Rest of the OSMC setup: SSH, SAMBA, LiRC config and config.txt (raspi-config) can be found in Add-Ons -&gt; OSMC Setup.

That's it, the mediacenter is up and running but if it's not supporting CEC feature of HDMI (like mine) than it's not possible to put it on standby. Inbuilt screensavers are only dimming or blanking the screen, but actually not turning off. There is a workaround: check this <a href="https://raspbianadventures.wordpress.com/2015/07/14/hdmi-monitor-standby-on-raspbian/" target="_blank">article</a>. Though there is still no standby (at least i don't know about it) on Raspberry Pi where you can bring back alive, with this workaround you can properly turn of the monitor. It suits my needs for now, since the Raspberry Pi is going to run all the time as home-automation instance.
