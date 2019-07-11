---
layout: post
title: 💻 Disable screen blanking in X
---

<a href="https://www.bianadventures.tk/wp-content/uploads/2015/07/tft-serial-18-rpi-1.jpg"><img class=" size-medium wp-image-104 aligncenter" src="https://www.bianadventures.tk/wp-content/uploads/2015/07/tft-serial-18-rpi-1.jpg?w=378" alt="tft-serial-18-rpi-1" width="378" height="360" /></a>

By default X in Raspbian is configured to blank the screen after 5 minutes of no input. If you are working on something it can be annoying. Also it do not turn off the screen, just blank it.

Here is how to disable it:

<!--more-->
<pre class="prettyprint">xset s off
xset dpms force on</pre>
&nbsp;
