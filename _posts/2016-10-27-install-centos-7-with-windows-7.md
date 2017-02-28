---
layout: post
title: install centos 7 with windows 7
date: 2016-10-27 00:23
author: 2freesky
comments: true
categories: [linux]
---
<ol>
	<li><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;"><span style="font-size:medium;">For my laptop, I need to change UEFI to Legacy in BIOS setup such that it is able to use usb bootable to install OS.</span></span></span></li>
	<li><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;"><span style="font-size:medium;">Create usb bootable with the help of Rufus</span></span></span></li>
	<li><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;"><span style="font-size:medium;">Install windows 7</span></span></span></li>
	<li><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;"><span style="font-size:medium;">Install cent os 7</span></span></span></li>
	<li><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;"><span style="font-size:medium;">In step '<em><strong>Installation Destination</strong></em>', choose hard drive and '<em><strong>I will configure partitioning</strong></em>'</span></span></span></li>
</ol>
<a href="https://2freesky.files.wordpress.com/2016/10/select-date-and-time-601x450.jpg"><img class="alignnone size-medium wp-image-502" src="https://2freesky.files.wordpress.com/2016/10/select-date-and-time-601x450.jpg?w=300" alt="select-date-and-time-601x450" width="300" height="200" /></a>
<a href="https://2freesky.files.wordpress.com/2016/10/centos-dual-device-selection1.jpg"><img class="alignnone size-medium wp-image-503" src="https://2freesky.files.wordpress.com/2016/10/centos-dual-device-selection1.jpg?w=300" alt="centos-dual-device-selection" width="300" height="106" /></a>
<ol start="6">
	<li>Manual partitioning page looks like next. We can see that there is ‘<em><strong>Unknown</strong></em>’ tab, this tab shows the windows partitions(This screen shot is from web and it displays existing linux partitions.).</li>
</ol>
In my case, the windows 7 has three drivers C, D and E. Wherein E is empty. So I
<ul>
	<li>select E and click the button ‘-’, marked 2 in picture, this will free this partition space.</li>
	<li>click button ‘+’, marked 3 in following diagram, this is used to create Cent OS partition. Create <strong>/</strong>, <strong>/boot</strong>, <strong>swap</strong> and <strong>/biosboot</strong> separately in pop up dialog</li>
	<li>Finally, click the ‘Done’ button and confirm the change dialog</li>
</ul>
<a href="https://2freesky.files.wordpress.com/2016/10/select-partitioning-scheme.png"><img class="alignnone size-medium wp-image-504" src="https://2freesky.files.wordpress.com/2016/10/select-partitioning-scheme.png?w=300" alt="select-partitioning-scheme" width="300" height="169" /></a>
<ol start="7">
	<li>Configure the ‘<em><strong>Network and Hostname</strong></em>’ like step 5 diagram</li>
	<li>Continue until install successful</li>
	<li>Install gnome desktop environment</li>
</ol>
<ul>
	<li>sudo yum groups install "GNOME Desktop"</li>
	<li>sudo systemctl set-default graphical.target</li>
</ul>
<ol start="10">
	<li>Add windows boot item</li>
</ol>
<ul>
	<li>sudo grub2-mkconfig &gt; /dev/null, it is used to check windows detected or not</li>
	<li>if windows can be found in previous step, run : sudo grub2-mkconfig -o /boot/grub2/grub.cfg</li>
</ul>
reboot the system.

Note : the diagrams in this article are from web.
