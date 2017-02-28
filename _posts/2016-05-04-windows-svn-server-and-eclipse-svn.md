---
layout: post
title: windows svn server and eclipse svn
date: 2016-05-04 22:08
author: 2freesky
comments: true
categories: [Uncategorized]
---
environment is :

windows 10

eclipse luna

VisualSVN-Server-3.5.2-x64

1.install VisualSVN Server

VisualSVN Server is a subversion server on Windows platform.

After installing, we can right click on the 'Repositories' to create a new repository with 'trunk','branch' and 'tag' structure.

If user want to set permission for corresponding directory, then she/he should right click on the folder and select 'properties' in context menu, eventually add group/user in 'Security' tab.

<a href="https://2freesky.files.wordpress.com/2016/05/visualsvn-server1.png"><img class="alignnone size-medium wp-image-146" src="https://2freesky.files.wordpress.com/2016/05/visualsvn-server1.png?w=300" alt="visualsvn-server" width="300" height="90" /></a>

User can access the repository by the url like above diagram as well as the ip without any configuration.

<strong>Note : when access the repository, the visualsvn server must not use vpn, otherwise other PC in LAN cannot access repository by ip or server name, or svn server cannot access repository by ip.</strong>

user or group can be added via right-clicking the 'Users' and 'Groups' in above picture.

2.put the project to repository

install svn in eclipse(my version is 3.0)

install svn connector like following diagram :

<a href="https://2freesky.files.wordpress.com/2016/05/image-provider.png"><img class="alignnone size-medium wp-image-147" src="https://2freesky.files.wordpress.com/2016/05/image-provider.png?w=267" alt="image-provider" width="267" height="300" /></a>

the final step is right click the project which you want to upload to repository, select 'Team' -&gt; Share Project, choose 'SVN'.

That's all!

&nbsp;

&nbsp;
