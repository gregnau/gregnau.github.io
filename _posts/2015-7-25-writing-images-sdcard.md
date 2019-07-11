---
layout: post
title: 💾 Writing .img images on SD-card
---

When i need to write an image on a sd-card, lately i choose <em>pv</em> instead <em>dd</em> in linux. Why? There is no need to bother with switches to achieve maximum writing speed of the sd-card and have progress bar, so i can know how much is left to finish.

<!--more-->

<em>PV</em><strong> method:</strong>
On most distros it is not installed by default, so you have to do it:
<pre class="prettyprint">sudo apt-get install pv</pre>
Then you can write images with the following:
<!--?prettify linenums=true?-->
<pre class="prettyprint">pv &lt; /path-to/image-file.img &gt; /dev/sdc</pre>
*** be sure to modify the <em>/dev/sdc</em> path at the end to your sd-card's path
<em>DD</em><strong> method:</strong>
There is no need to install, it's integrated on almost every linux distro.

Same command as above but with <em>dd</em> looks like:
<!--?prettify linenums=true?-->
<pre class="prettyprint">dd bs=4M if=/path-to/image-file.img of=/dev/sdc</pre>
*** be sure to modify the <em>/dev/sdc</em> path at the end to your sd-card's path

You can adjust the "speed" of writing with the <em>bs=4M</em> option, where lets say the <em>4M</em> is the rating of your sd-card. So for example i have a Sandisk Ultra-something 16GB Class 10 sd-card and i'm using <em>10M</em> as option. Roughly... More like <em>8M</em> was stable if i remember good ;)
