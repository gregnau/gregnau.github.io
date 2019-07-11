---
layout: post
title: 🔥 Essent E-Thermostate control from Linux CLi
---

<a href="https://www.bianadventures.tk/wp-content/uploads/2015/07/slimmethermostaatjpg.jpg"><img class=" size-full wp-image-114 aligncenter" src="https://www.bianadventures.tk/wp-content/uploads/2015/07/slimmethermostaatjpg.jpg" alt="E-Thermostaat" width="360" height="254" /></a>

The E-Thermostaat from the ICY or Essent is one of the first IoT thermostates with web interface and native Android/iOS apps. Featuring multiple temperature presets, weekly schedule with many timers and also detailed weekly history of temperatures and on/off times.<!--more--> Though the latter unfortunately only available through the web interface. It's been mostly sold from 2013 till 2015 in The Netherlands but possibly also available in the whole BeNeLux.

Despite the broad number of useful features and the nice native applications, sometimes it comes handy to be able to control it from Linux devices. Like a home-automation or domotica system. It is using simple HTTP requests for communication with the server, easily controllable with <code>curl</code>. Here is a brief manual about the task:

<ol><li>Login and get a session token:
<pre class="prettyprint">curl -s --request POST 'https://portal.icy.nl/login' --data 'username=[USERNAME]&password=[PASSWORD]'</pre>
Replace <code>[USERNAME]</code> and <code>[PASSWORD]</code> with the actual E-Thermostaat login credentials.
&nbsp;
This should produce similar output as below, ending with message <code>200/OK</code>. Otherwise the response code and message gives a hint about the possible issue.
<pre class="prettyprint">
{"userid":"0123456789","username":"[USERNAME]","email":
"example@email.com","mailing":0,"gender":1,"name":
"Example","preposition":"","lastname":"Example",
"serialhub":"1234567890123","serialthermostat1":
"9876543210987","token":"1ab234cde5fghi6j7kl8mn9012",
"status":{"code":200,"message":"OK"}}</pre>
Take note of the <code>"serialthermostat1"</code> and <code>"token"</code>, both of them is necessary to read and control the device.</li>
<li>Request the current config data (settings,temperatures,etc):
<pre class="prettyprint">curl -s -H "Session-token:[TOKEN]" --request GET 'https://portal.icy.nl/data' --data 'username=[USERNAME]&password=[PASSWORD]'</pre>
Replace here again the <code>[USERNAME]</code> and <code>[PASSWORD]</code> with the login credentials, but also copy the <code>"token"</code> from the previous output.
&nbsp;
Example config data:
<pre class="prettyprint">
{"uid":"9876543210987","first-seen":"2014-01-15 18:17:1
2","last-seen":"2015-02-24 20:30:40","device-status":0,
"temperature1":20,"temperature2":19,"day-clock":[65535],
"week-clock":[65535,65535,65535,65535,65535,65535,65535,
65535,65535,65535,65535,65535,65535,65535,65535,65535,
65535,65535,65535,65535,65535,65535,65535,65535,65535,
65535,65535,65535,65535,65535],"configuration":[0,5,12,
12,20,30,40,60,30,1,1,1],"status":
{"code":200,"message":"OK"}}</pre></li>
<li>Modify config (set temperature,install timer, etc):
<pre class="prettyprint">curl -s -H "Session-token:[TOKEN]" --request POST https://portal.icy.nl/data --data "uid=[UID]&temperature1=[TEMPERATURE]"</pre>
There is no more login credentials necessary for making changes in the config, but the <code>[TOKEN]</code> and <code>[UID]</code> needed along with a setting(s) to change. Like the above example which is the goal temperature of the thermostate.</li></ol>
