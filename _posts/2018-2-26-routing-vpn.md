---
layout: post
title: 🔒 Routing specific traffic through VPN
---

Virtual Private Networks are crucial things in privacy or security and most of them are configured ready to use. Although by default those are routing everything through the VPN. That is not ideal in every case, sometimes it is enough to route only a part of the traffic and leave the rest on the physical interface. Taking examples of different cases this tutorial describes the steps needed to route specific traffic only over the VPN.
<!--more-->
<h3>OpenVPN</h3>
This must be properly installed and configured to act as the VPN client. No providers application can be used here.
<ol>
<li>Install OpenVPN and its dependencies:
<pre class="prettyprint">sudo apt-get install openvpn</pre>
</li>

<li>Download <code>*.ovpn</code> config files from the provider's site. These are usually available to download as a compressed file.</li><br>

<li>Extract files in a subfolder of <code>/etc/openvpn/</code>. It is important to put those files in a subfolder, otherwise they gets interpreted as config files when starting the systemd service.
<pre class="prettyprint">sudo unzip ovpn_files.zip -d /etc/openvpn/servers</pre></li>

<li>Now all these files needs to be updated with the <code>route-nopull</code> option.
This is to tell OpenVPN not to route anything, just create the connection and the interface.
<pre class="prettyprint">for file in /etc/openvpn/servers/* ; do echo -e "\nroute-nopull" >> $file; done</pre></li>

<li>Starting the VPN automatically on boot requires also to have the username and password preconfigured,
so it doesn't ask for it during the initialization of the connection. A single file is needed with <code>username</code> and <code>password</code> in 2 lines.
<pre class="prettyprint">sudo nano /etc/openvpn/.secret</pre></li>

The file content should look something like this:
<pre class="prettyprint">username
password</pre>

When done editing, don't forget to change permissions properly:
<pre class="prettyprint">sudo chmod 600 /etc/openvpn/.secret</pre>

<li>Softlink one of the config files from the <code>subfolder</code> to the <code>openvpn</code> folder, but with a <code>*.conf</code> extension. This connection will be initialized when the systemd service has been started.
<pre class="prettyprint">sudo ln -s /etc/openvpn/servers/nl123.vpn-provider.tcp.ovpn /etc/openvpn/nl123.vpn-provider.tcp.conf</pre></li>

<li>The OpenVPN service can be started now. It is going to create a (probably) <code>tun0</code> adapter, but not doing any routing so still unusable.
<pre class="prettyprint">sudo service openvpn start</pre>

Check if it is really up and running:
<pre class="prettyprint">ps aux | grep openvpn</pre>
<pre class="prettyprint">ifconfig tun0</pre></li>

<li>Create a new routing table for the purpose by adding an extra line with the tables name in the file <code>rt_tables</code>.
<pre class="prettyprint">echo -e '\n200\tmy_table' | sudo tee --append /etc/iproute2/rt_tables > /dev/null</pre></li></li>

<li>Define a new default route and gateway to the new connection. The gateway <code>1.2.3.4</code> have to be replaced with the <code>tun0</code>'s gateway. This is usually the ip address of the interface, but the last group of numbers is <code>1</code>. Like <code>1.2.3.1</code>
<pre class="prettyprint">sudo ip route add default via 1.2.3.4 dev tun0 table my_table</pre></li>

<li>Add a new rule to the ip table for redirecting marked packages on the above defined route:
<pre class="prettyprint">sudo ip rule add fwmark 0x1 table my_table</pre></li>

<li>Disable the reverse path filtering on all interface to let the routing work with this config:
<pre class="prettyprint">for f in /proc/sys/net/ipv4/conf/*/rp_filter; do echo 0 > $f; done
echo 1 > /proc/sys/net/ipv4/route/flush</pre></li>
</ol>

The VPN is already usable for now, but only by applications which supports a bind option. Since not all of them have one here comes two options for redirecting traffic on the VPN.

<h3>Redirect based on port</h3>
<ol>
<li>Mark the packets on specific port to be redirected, for example the http port 80:
<pre class="prettyprint">sudo iptables -A OUTPUT -t mangle -o eth0 -p tcp --dport 80 -j MARK --set-mark 1</pre></li>

<li>Redirect every packet with a mark and rewrite the source ip address. Don't forget to replace the ip <code>1.2.3.4</code>!
<pre class="prettyprint">sudo iptables -A POSTROUTING -t nat -o tun0 -p tcp --dport 80 -j SNAT --to 1.2.3.4</pre></li>
</ol>

<h3>Redirect based on multiple ports range</h3>
<ol>
<li>Mark the packets on specific port-range to be redirected, for example the ports between 6884-6889:
<pre class="prettyprint">sudo iptables -A OUTPUT -t mangle -o eth0 -p tcp --match multiport --dports 6884:6889 -j MARK --set-mark 1</pre></li>

<li>Redirect every packet with a mark and rewrite the source ip address. Don't forget to replace the ip <code>1.2.3.4</code>!
<pre class="prettyprint">sudo iptables -A POSTROUTING -t nat -o tun0 -p tcp --match multiport --dports 6884:6889 -j SNAT --to 1.2.3.4</pre></li>
</ol>

<h3>Redirect based on user</h3>
<ol>
<li>Mark the packets from a specific user to be redirected. Don't forget to change the user-id <code>1002</code>:
<pre class="prettyprint">sudo iptables -A OUTPUT -t mangle -o eth0 -p all -m owner --uid-owner 1002 -j MARK --set-mark 1</pre></li>

<li>Redirect every packet with a mark and rewrite the source ip address. Don't forget to replace the ip <code>1.2.3.4</code>!
<pre class="prettyprint">sudo iptables -A POSTROUTING -t nat -o tun0 -p all -j SNAT --to 1.2.3.4</pre></li>
</ol>

<h3>Ready to test</h3>
After it is all set-up, possible to check if it properly working by issuing the following:
<pre class="prettyprint">tcpdump -i tun0 -n</pre>
Or by simply checking the current ip:
<pre class="prettyprint">curl icanhazip.com</pre>
