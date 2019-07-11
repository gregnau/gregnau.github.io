---
layout: post
title: 💾 Software RAID-1 setup guide
---

Nowadays the huge capacity HDD prices are gone quite affordable, that basically everybody can have many gigabytes of storage space in a personal computer or even in a laptop. This means tons of precious images and recordings or bookkeeping of a small bussines.
<!--more-->
<blockquote>
<h5 style="text-align: center; margin-left: 20%; margin-right: 20%;">"Data is a precious thing and will last longer than the systems themselves."
<span style="font-size: 10px;">Tim Berners-Lee</span>
<span style="font-size: 8px;">(Inventor of the World Wide Web)</span></h5>
</blockquote>
There is an affordable way of protecting it against data loss with properly configured software raid level 1. This way both HDDs will store the same data automatically and in case of failure the computer can stay up and running without any loss of data. It is not a performance hog at all, runs smooth and silent in the background.

Let's see how I set up on my home computer/server with two 1 terabyte hard-disks:
<div style="background: #e51c23; margin-left: 10%; margin-right: 10%; color: white; padding: 10px; padding-left: 20px; border-radius: 20px;">Warning:
This guide involves direct modification of the HDD partitions so therefore proceed with caution and on your responsibility.</div>
&nbsp;

Assuming 2 identical/similar and empty HDDs which are both empty and unpartitioned in a (U)EFI compatible system. Make sure to have a backup all of the important data before begin.
<ol>
 	<li>Boot up a simple live distro like a Xubuntu, or anything else preferably with <code>parted</code> preinstalled.</li>
 	<li>Create new GPT partition table on each hard-disk.
<pre class="prettyprint">parted /dev/sda mklabel gpt
parted /dev/sdb mklabel gpt</pre>
</li>
 	<li>Create the same partitions on both disk: <code>EFI, boot, root, home, swap</code>.
<pre class="prettyprint">parted /dev/sda mkpart primary fat32 1MB 513MB
parted /dev/sda set 1 boot on
parted /dev/sda set 1 esp on
parted /dev/sda mkpart primary ext4 513MB 1025MB
parted /dev/sda mkpart primary ext4 1025MB 33GB
parted /dev/sda mkpart primary ext4 33GB 927GB
parted /dev/sda mkpart primary linux-swap 927GB 931GB</pre>
<pre class="prettyprint">parted /dev/sdb mkpart primary fat32 1MB 513MB
parted /dev/sdb set 1 boot on
parted /dev/sdb set 1 esp on
parted /dev/sdb mkpart primary ext4 513MB 1025MB
parted /dev/sdb mkpart primary ext4 1025MB 33GB
parted /dev/sdb mkpart primary ext4 33GB 927GB
parted /dev/sdb mkpart primary linux-swap 927GB 931GB</pre>
</li>
 	<li>Build filesystems on the fresh partitions.
<pre class="prettyprint">mkfs.vfat /dev/sda1
mkfs.ext4 /dev/sda2
mkfs.ext4 /dev/sda3
mkfs.ext4 /dev/sda4</pre>
<pre class="prettyprint">mkfs.vfat /dev/sdb1
mkfs.ext4 /dev/sdb2
mkfs.ext4 /dev/sdb3
mkfs.ext4 /dev/sdb4</pre>
</li>
 	<li>Install Linux Software Raid package.
<pre class="prettyprint">apt-get install mdadm</pre>
</li>
 	<li>Create the RAID level 1 arrays.
<pre class="prettyprint">mdadm --create /dev/md0 --level 1 --raid-devices 2 /dev/sd[ab]2
mdadm --create /dev/md1 --level 1 --raid-devices 2 /dev/sd[ab]3
mdadm --create /dev/md2 --level 1 --raid-devices 2 /dev/sd[ab]4</pre>
</li>
 	<li>Wait until it's done syncing.
<pre class="prettyprint">watch cat /proc/mdstat</pre>
</li>
 	<li>Install the OS on the newly created <code>md#</code> partitions.
<pre class="prettyprint">md0 - /boot
md1 - /
md2 - /home</pre>
At least it works like this on Debian and Ubuntu, but not on Linux Mint. The following steps was necessary, assuming the graphical installer is launched from the live desktop:</li>
 	<li>After the installation is finished, do not select reboot yet. Instead open a terminal!
<pre class="prettyprint">mount --bind /dev /target/dev
mount -t proc proc /target/proc
mount -t sysfs sys /target/sys
chroot target
apt-get install mdadm
apt-get install --reinstall grub2
exit</pre>
</li>
 	<li>Reboot the computer now and enjoy RAID level 1.</li>
</ol>
