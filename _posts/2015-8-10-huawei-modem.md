---
layout: post
title: 📱 Huawei E1752 on Linux
---

The Huawei E1752 is an <del>ancient</del> USB HSDPA modem which can be still used for connecting to the internet or even sending and receiving sms messages. This post is about to get it working on Raspbian/Jessie to use it as a backup internet connection and sms-remote control my home. One would expecting it to be more difficult, but it's very easy to install or use.

<!--more-->

So here are the steps i done:

Do not connect your usb modem yet, first you need to install the <em>usb-modeswitch</em> and <em>gsm-utils package</em>. These are both necessary for the modem to function properly and support sms.
<pre class="prettyprint">sudo apt-get install usb-modeswitch gsm-utils</pre>
Now edit the <em>/etc/usb_modeswitch<code></code>.conf</em> and paste the following lines:
<pre class="prettyprint"># Huawei E1752 #
DefaultVendor= 0x12d1
DefaultProduct= 0x1446TargetVendor= 0x12d1
TargetProduct= 0x1001
MessageEndpoint= 0x01
MessageContent="55534243000000000000000000000011060000000000000000000000000000"
HuaweiMode= 1
</pre>
Reboot

It's time to plug in the Huawei USB Modem and it's going to appear under <em>/dev/ttyUSBx</em>

Test the modem with sending an sms:
<pre class="prettyprint">echo "SMS message" | gsmsendsms -d /dev/ttyUSB0 -b 19200 PHONE_NUMBER</pre>
There should be no output if everything went fine and the sms is going to arrive in a few seconds.

To be continued with the internet config...
