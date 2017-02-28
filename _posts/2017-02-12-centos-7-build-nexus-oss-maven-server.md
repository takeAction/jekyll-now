---
layout: post
title: centos 7 build nexus oss maven server
date: 2017-02-12 22:53
author: 2freesky
comments: true
categories: [linux, maven]
---
<h1>Installation</h1>
<ol>
 	<li>install oracle idk 8 and configure JAVA_HOME, make it as default</li>
 	<li>download nexus oss 3.2</li>
 	<li>
[code]sudo tar xvzf nexus-3.2.0-01-unix.tar.gz -C /opt[/code]
</li>
 	<li>
[code]sudo chmod -R 777 sonatype-work[/code]
</li>
 	<li>to start it from terminal manually, just enter nexus installation home/bin, then run command : 
[code]./nexus run[/code]
.  In this case, we need to press ctrl+c keys to stop it.</li>
 	<li>Then we can access it by : 
[code]http://localhost:8081[/code]
</li>
</ol>
<h1>Make it as a service</h1>
In order to make it start automatically after server reboot, we need to make it as service.

It is a good practice to run that service as a specific user that has only required access rights rather than root user.

We can configure the repository manager to run as a service with init.d or systemd. Both are startup frameworks used in Linux-based systems such as Ubuntu and CentOS.
<ul>
 	<li>
[code]sudo vi /opt/nexus/bin/nexus.rc[/code]
</li>
 	<li>make previous file looks like 
[code]run_as_user=&quot;&lt;linux user&gt;&quot;[/code]
</li>
 	<li>
[code]sudo ln -s /opt/nexus/bin/nexus /etc/init.d/nexus[/code]
</li>
 	<li>
[code]cd /etc/init.d
sudo chkconfig --add nexus
sudo chkconfig --levels 345 nexus on
sudo service nexus start[/code]

<div class="page" title="Page 35">
<div class="layoutArea">
<div class="column">

The second command adds nexus as a service to be started and stopped with the command. chkconfig manages the symbolic links in /etc/rc[0-6].d which control the services to be started and stopped when the operating system restarts or transitions between run-levels. The third command adds nexus to run-levels 3, 4, and 5. Then the service command starts the repository manager.

</div>
</div>
</div></li>
</ul>
<h1>Access it from other computers</h1>

[code]sudo firewall-cmd --zone=public --permanent --add-service=http[/code]



[code]sudo firewall-cmd --zone=public --add-port=8081/tcp --permanent[/code]



[code]sudo firewall-cmd --reload[/code]


&nbsp;

&nbsp;
