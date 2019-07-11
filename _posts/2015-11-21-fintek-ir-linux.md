---
layout: post
title: 📡 Getting the Fintek IR receiver to work with Linux
---

<a href="https://www.bianadventures.tk/wp-content/uploads/2017/06/remotes.jpg"><img class="size-full wp-image-272 aligncenter" src="https://www.bianadventures.tk/wp-content/uploads/2017/06/remotes.jpg" alt="" width="180" height="154" /></a>
Some of the Medion AIO (all-in-one) desktop pc-s are equipped with IR remote receiver, which doesn't work out of the box on linux. Instead during boot it gives the following error:<!--more-->
<pre class="prettyprint">[    6.925985] fintek-cir 00:06: IR PNP Port not valid!</pre>

Quite annoying, but here is a workaround for it. It's not really a fix, because the error message still appears at boot, but the IR receiver is going to work afterwards.

<ol>
<li>
Unload the module called <code>fintek-cir</code>:
<pre class="prettyprint">modprobe -r fintek-cir</pre>
</li>
<li>
Fix the drivers IO mapping:
<pre class="prettyprint">echo "auto" > /sys/bus/acpi/devices/FIT0002:00/physical_node/resources</pre>
</li>
<li>
Re-load the module <code>fintek-cir</code>:
<pre class="prettyprint">modprobe fintek-cir</pre>
</li>
</ol>
&nbsp;
That's it, the IR receiver should work now. You can check the <code>tail</code> of <code>dmesg</code> to make sure it's fine.
&nbsp;
Now to automate this process at boot, the following steps were necessary:
<ol>
<li>
Create a file with the following content:
<pre class="prettyprint">#!/bin/sh
/sbin/modprobe -r fintek-cir
echo "auto" > "/sys/bus/acpi/devices/FIT0002:00/physical_node/resources"
/sbin/modprobe fintek-cir</pre>
Save it somewhere, like <code>/etc/fintek-cir.sh</code>.
</li>
<li>
Mark it as executable:
<pre class="prettyprint">chmod +x /etc/fintek-cir.sh</pre>
</li>
<li>
Add an entry to the crontab (root):
<pre class="prettyprint">@reboot /etc/fintek-cir.sh</pre>
</li>
</ol>
&nbsp;
The receiver should work after every (re)boot, LIRC can be installed now.
