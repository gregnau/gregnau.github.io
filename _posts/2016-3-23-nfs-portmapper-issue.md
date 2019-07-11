---
layout: post
title: 💾 NFS not starting, because portmapper is not running
---

Recently i've faced some issues with <code>nfs-kernel-server</code> and <code>nfs-common</code> exiting during startup. It is very annoying, since until the above services not restarted after every (re)boot there is no file sharing. The reason is that the services are not started in proper order and nfs services would rely on the portmapper (<code>rpcbind</code>) service. If you see the same warning about <code>portmapper</code> as on the screenshot, then read further about how to fix it.
<!--more-->
<a href="https://www.bianadventures.tk/wp-content/uploads/2017/01/screenshot_2017-01-03_16-58-02.png"><img class="alignnone size-medium wp-image-140" src="https://www.bianadventures.tk/wp-content/uploads/2017/01/screenshot_2017-01-03_16-58-02.png?w=640" alt="Screenshot of portmapper/nfs issue" width="640" height="154" /></a>

<ol><li>Runlevel needs to be modified in the <code>nfs-common</code> and <code>rpcbind</code> init files:
<pre class="prettyprint">/etc/init.d/nfs-common
/etc/init.d/rpcbind
</pre></li>

<li>Replace the line around the top:
<pre class="prettyprint"># Default-Start:     S</pre>
with this line:
<pre class="prettyprint"># Default-Start:     2 3 4 5</pre>
</li>
<li>Save it. Then the changed init scripts needs to be removed from <code>rc.d</code>, otherwise updating is going to fail.
<pre class="prettyprint">update-rc.d -f rpcbind remove
update-rc.d rpcbind defaults

update-rc.d -f nfs-common remove
update-rc.d nfs-common defaults</pre></li></ol>

<ul><li>Just to be sure do the nfs-kernel-server package also (at least i did):
<pre class="prettyprint">update-rc.d -f nfs-kernel-server remove
update-rc.d nfs-kernel-server defaults</pre></li></ul>

That's it, reboot and nfs should be up and running.

Source(s):
<a href="http://raspberrypi.stackexchange.com/questions/10403/nfs-server-not-starting-portmapper-is-not-running" target="_blank">NFS Server: Not starting: portmapper is not running</a>
