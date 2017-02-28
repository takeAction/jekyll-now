---
layout: post
title: mcaos sierra install tomcat 8
date: 2017-01-02 23:04
author: 2freesky
comments: true
categories: [Uncategorized]
---
<ul>
	<li>download tomcat</li>
	<li>unzip it by double click</li>
	<li>change ownership and permission by sudo chown -R and sudo chmod +x tomcat/bin/*.sh, otherwise error thrown:
<p class="p1"><span class="s1">/Library/Tomcat/bin/startup.sh</span></p>
<p class="p1"><span class="s1">-bash: /Library/Tomcat/bin/startup.sh: Permission denied</span></p>
</li>
	<li>start/stop tomcat by running startup.sh/shutdown.sh in bin</li>
	<li>optionally, we can move tomcat to /usr/local and create a soft link in /Library to point to /usr/local/tomcat</li>
</ul>
