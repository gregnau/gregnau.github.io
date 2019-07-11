---
layout: post
title: 💻 How to install XFCE + Compiz + Emerald on Debian 8 (Jessie) and 9 (Stretch)
---

There is no doubt about the power of the command line, though sometimes it is good to have a nice desktop at hand. Here comes XFCE in the picture, since it is a well proven lightweight desktop environment, my favorite. Ofcourse a lean desktop is a bit sour nowadays, effects and feedback is expected from a decent system. The compositor named Compiz meant to help that with tons of flashy effect plugins.<!--more--> These plugins range from simple actions to incredible effects like exposing all the windows on the screen or blow a window to particles at closing. When it is properly configured to use the hardware acceleration of the VGA, then these effects have no impact on performance or cpu time. Emerald is the windows decorator, because Compiz not working well together with the default <code>xfwm4</code> (the default window-manager of XFCE). Installation should be straightforward, but since Debian 9 the Compiz packages have been updated to the much subdued 0.9 version which (by the time) lacks the flashy effect plugins. Fortunately there were people who took the efforts and made the good-old 0.8 version of the compiz with all of it's plugins available as repository for Debian. Let's see how to setup them in order:
<h1>XFCE:</h1>
<ol>
 	<li>The meta-package <code>task-xfce-desktop</code> will install a base XFCE desktop along with the LightDM display (&amp; graphical login) manager:
<pre class="prettyprint">sudo apt-get install task-xfce-desktop</pre>
</li>
 	<li>Restart the computer and make sure desktop is installed fine:
<pre class="prettyprint">sudo reboot</pre>
</li>
</ol>
<h3>Optional steps</h3>
<ul>
 	<li>
<h5>Get Themes:</h5>
The basic theme-packs are available from the official repositories:
<pre class="prettyprint">apt-cache search gtk-theme</pre>
</li>
 	<li>
<h5>Get Icons:</h5>
The basic icon-packs are available from the official repositories:
<pre class="prettyprint">apt-cache search icon-theme</pre>
</li>
 	<li>
<h5>Disable LightDM:</h5>
In case the graphical login manager not needed, it can be disabled with:
<pre class="prettyprint">sudo systemctl disable lightdm.service</pre>
</li>
 	<li>
<h5>Re-Enable LightDM:</h5>
To re-enable the graphical login manager:
<pre class="prettyprint">sudo dpkg-reconfigure lightdm</pre>
</li>
</ul>
<h1>Emerald</h1>
<ol>
 	<li>It should install automatically with the Compiz in the next section.
In case it's not, than it can be installed with the following line:
<pre class="prettyprint">sudo apt-get install emerald emerald-themes</pre>
</li>
</ol>
<h3>Optional steps</h3>
<ul>
 	<li>
<h5>Themes</h5>
<a href="https://github.com/shimmerproject/Greybird" target="_blank" rel="noopener noreferrer">Greybird</a> theme is a good choice available on many platforms (gtk-2/3,metacity,emerald,etc).
<pre class="prettyprint">sudo apt-get install greybird-gtk-theme</pre>
It needs to be imported manually into Emerald from <code>/usr/share/themes/Greybird/Greybird.emerald</code>
<pre class="prettyprint">emerald-theme-manager</pre>
</li>
</ul>
<h1>Compiz</h1>
<h3>Debian 8 (Jessie):</h3>
<ol>
 	<li>Add the repository to the apt sources-list:
<pre class="prettyprint">echo 'deb http://download.opensuse.org/repositories/home:/stevenpusser:/compiz-reloaded/Debian_8.0/ /' | sudo tee /etc/apt/sources.list.d/compiz-reloaded.list</pre>
</li>
 	<li>Authenticate the source:
<pre class="prettyprint">wget -nv http://download.opensuse.org/repositories/home:stevenpusser:compiz-reloaded/Debian_8.0/Release.key -O - | sudo apt-key add -</pre>
</li>
 	<li>Install Compiz-Reloaded with this command:
<pre class="prettyprint">sudo apt-get update &amp;&amp; sudo apt-get install compiz</pre>
</li>
</ol>
<h3>Debian 9 (Stretch):</h3>
<ol>
 	<li>Add the repository to the apt sources-list:
<pre class="prettyprint">echo 'deb http://download.opensuse.org/repositories/home:/stevenpusser:/compiz-reloaded/Debian_9.0/ /' | sudo tee /etc/apt/sources.list.d/compiz-reloaded.list</pre>
</li>
 	<li>Authenticate the source:
<pre class="prettyprint">wget -nv http://download.opensuse.org/repositories/home:stevenpusser:compiz-reloaded/Debian_9.0/Release.key -O - | sudo apt-key add -</pre>
</li>
 	<li>Install Compiz-Reloaded with this command:
<pre class="prettyprint">sudo apt-get update &amp;&amp; sudo apt-get install compiz</pre>
</li>
</ol>
<h1>Test &amp; Configure Compiz</h1>
<ul>
 	<li>
<h3>Test</h3>
Compiz along with all the plugins and dependencies should be installed now, but not yet running or set as default. To test it out press <code>ALT+F2</code> and run this command:
<pre class="prettyprint">compiz --replace</pre>
There will be no window-decorator, but the desktop should look like as it was.

To test the windows decorator, run the following command:
<pre class="prettyprint">emerald --replace</pre>
</li>
&nbsp;
 	<li>
<h3>Configuration</h3>
It can be done with a simple and an advanced interface, it depends on which you prefer. Though for the nicest custom effects the advanced config is recommended :)
&nbsp;
Simple configuration:
<pre class="prettyprint">simple-ccsm</pre>
<a href="https://www.bianadventures.tk/wp-content/uploads/2017/03/simple-ccsm.png"><img class="alignnone size-medium wp-image-311" src="https://www.bianadventures.tk/wp-content/uploads/2017/03/simple-ccsm-234x300.png" alt="" width="234" height="300" /></a>
&nbsp;
Advanced Configuration:
<pre class="prettyprint">ccsm</pre>
<a href="https://www.bianadventures.tk/wp-content/uploads/2017/03/ccsm.png"><img class="alignnone size-medium wp-image-312" src="https://www.bianadventures.tk/wp-content/uploads/2017/03/ccsm-300x174.png" alt="" width="300" height="174" /></a>
&nbsp;
If everything looks fine, than it can be set as default from next boot.</li>
 	<li>
<h3>Setting as default</h3>
First the window-decorator should be set on the advanced compiz configuration to launch on startup. The start of the compositor can be set per user session or system-wide, it's up to you which one you prefer!
<h5>Set Window-decorator:</h5>
<ol>
 	<li>Launch the Compiz-Config-Settings-Manager:
<pre class="prettyprint">ccsm</pre>
</li>
 	<li>Navigate to <code>Effects &gt; Window Decoration</code>, enable it and replace <code>Command</code> with:
<pre class="prettyprint">emerald --replace</pre>
Next time it is going to launch with Compiz.</li>
</ol>
<h5>Set Compiz as User session default:</h5>
<pre class="prettyprint">cp /etc/xdg/xfce4/xfconf/xfce-perchannel-xml/xfce4-session.xml ~/.config/xfce4/xfconf/xfce-perchannel-xml/</pre>
Then replace <code>Client0_Command</code> value with <code>compiz --replace</code> to look something like as below:
<pre class="prettyprint">&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;channel name="xfce4-session" version="1.0"&gt;
  &lt;property name="general" type="empty"&gt;
    &lt;property name="FailsafeSessionName" type="string" value="Failsafe"/&gt;
  &lt;/property&gt;
  &lt;property name="sessions" type="empty"&gt;
    &lt;property name="Failsafe" type="empty"&gt;
      &lt;property name="IsFailsafe" type="bool" value="true"/&gt;
      &lt;property name="Count" type="int" value="5"/&gt;
      &lt;property name="Client0_Command" type="array"&gt;
        &lt;value type="string" value="compiz --replace"/&gt;
      &lt;/property&gt;
      &lt;property name="Client0_PerScreen" type="bool" value="false"/&gt;
      &lt;property name="Client1_Command" type="array"&gt;
        &lt;value type="string" value="xfsettingsd"/&gt;
      &lt;/property&gt;
      &lt;property name="Client1_PerScreen" type="bool" value="false"/&gt;
      &lt;property name="Client2_Command" type="array"&gt;
        &lt;value type="string" value="xfce4-panel"/&gt;
      &lt;/property&gt;
      &lt;property name="Client2_PerScreen" type="bool" value="false"/&gt;
      &lt;property name="Client3_Command" type="array"&gt;
        &lt;value type="string" value="Thunar"/&gt;
        &lt;value type="string" value="--daemon"/&gt;
      &lt;/property&gt;
      &lt;property name="Client3_PerScreen" type="bool" value="false"/&gt;
      &lt;property name="Client4_Command" type="array"&gt;
        &lt;value type="string" value="xfdesktop"/&gt;
      &lt;/property&gt;
      &lt;property name="Client4_PerScreen" type="bool" value="false"/&gt;
    &lt;/property&gt;
  &lt;/property&gt;
  &lt;property name="splash" type="empty"&gt;
    &lt;property name="Engine" type="string" value=""/&gt;
  &lt;/property&gt;
&lt;/channel&gt;</pre>
</li>
 	<li><h5>Set Compiz as System-wide default:</h5>
Edit the file <code>/etc/xdg/xfce4/xfconf/xfce-perchannel-xml/xfce4-session.xml</code> just like above at the per user session process:
<pre class="prettyprint">sudo nano /etc/xdg/xfce4/xfconf/xfce-perchannel-xml/xfce4-session.xml</pre>
</li>
</ul>
&nbsp;
&nbsp;
Now you should have a decent desktop with lot of customization options. If there is any question i'm happy to answer below in the comments.
&nbsp;
&nbsp;
Resources:
<a href="http://forums.debian.net/viewtopic.php?t=133561" target="_blank" rel="noopener noreferrer">http://forums.debian.net/viewtopic.php?t=133561</a>
