---
layout: post
title: 💻 HDMI monitor standby on Raspbian
---

<img class="aligncenter" src="http://www.it-echo.com/wp-content/uploads/2009/08/gateway_fhd2401.jpg" alt="" width="292" height="285" />

Last week project was to get a cheap TV in the bedrooms of the house, but cheap things have cons of course. After some research a HDMI monitor hooked on Raspberry Pi seemed the solution.
It was a good choice, though i had a hard time to put the screen standby together. Since there is no working screen standby in XBMC/Kodi for the Raspi. In case you use the "<em>tvservice</em>" command then you have to restart the XBMC/Kodi, to get out of blank screen.

<!--more-->

I was using the following python snippet, because this way there is nothing pops up (like terminal) and it's just works.

[code language="python"]
#!/usr/bin/python

import subprocess
import string
import os.path

if (os.path.isfile(&amp;quot;/tmp/screen.state&amp;quot;) == False):
subprocess.call( &amp;quot;touch /tmp/screen.state&amp;quot;, shell=True)

result = subprocess.check_output(&amp;quot;cat /tmp/screen.state&amp;quot;, shell=True)

if (result.find (&amp;quot;f&amp;quot;) &amp;amp;gt;0 ):
subprocess.call( &amp;quot;/opt/vc/bin/vcgencmd display_power 1&amp;quot;, shell=True )
subprocess.call( &amp;quot;echo 'On' &amp;amp;gt; /tmp/screen.state&amp;quot; , shell=True )
else:
subprocess.call( &amp;quot;/opt/vc/bin/vcgencmd display_power 0&amp;quot;, shell=True )
subprocess.call( &amp;quot;echo 'Off' &amp;amp;gt; /tmp/screen.state&amp;quot;, shell=True )
[/code]

Save it as <em>sleepstate.py</em> in your home directory:

[code language="bash"]nano ~/sleepscreen.py[/code]

Then it's possible to call the script from XBMC/Kodi:

[code language="xml"]RunScript(&amp;quot;/home/osmc/sleepscreen.py&amp;quot;)[/code]

For example to map it on the power button of the remote,
create a <em>keymap.xml</em> in your <em>.kodi/userdata/keymaps</em>:

[code language="bash"]nano ~/.kodi/userdata/keymaps/keymap.xml[/code]

Then paste the following example:

[code language="xml"]
&amp;lt;keymap&amp;gt;
&amp;lt;global&amp;gt;
&amp;lt;remote&amp;gt;
&amp;lt;red&amp;gt;RunScript(&amp;quot;/home/osmc/sleepstate.py&amp;quot;)&amp;lt;/red&amp;gt;
&amp;lt;/remote&amp;gt;
&amp;lt;/global&amp;gt;
&amp;lt;/keymap&amp;gt;
[/code]
