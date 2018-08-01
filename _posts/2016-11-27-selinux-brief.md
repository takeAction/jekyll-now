---
layout: post
title: SELinux brief
date: 2016-11-27 23:30
author: 2freesky
comments: true
categories: [Linux]
---

<ul>
	<li>Security Enhanced Linux which is a mandatory access control(MAC) security mechanism which based on defined policies.  User can disable it.</li>
</ul>
<ul>
	<li>As we know, users can control the permissions of files that they own by setting read, write and execute for specified user, group and others.  This is called discretionary access control(DAC). MAC and DAC make up the Linux security.  The flow is</li>
</ul>
<a href="https://2freesky.files.wordpress.com/2016/11/screenshot-from-2016-11-27-23-07-23.png"><img class="alignnone size-medium wp-image-516" src="https://2freesky.files.wordpress.com/2016/11/screenshot-from-2016-11-27-23-07-23.png?w=192" alt="screenshot-from-2016-11-27-23-07-23" width="192" height="300" /></a>
<ul>
	<li>SELinux has three modes: Enforcing is the default mode which will enable and enforce the SELinux security policy on the system, this will deny illegal access and log it. Permissive mode just log the actions without denying. Disabled mode means turn off the SELinux.  We can edit these modes by modifying /etc/sysconfig/selinux</li>
</ul>
Users may use the 'sestatus' command to view the current SELinux status:
<blockquote>$ sestatus
SELinux status: enabled
SELinuxfs mount: /sys/fs/selinux
SELinux root directory: /etc/selinux
Loaded policy name: targeted
Current mode: enforcing
Mode from config file: enforcing
Policy MLS status: enabled
Policy deny_unknown status: allowed
Max kernel policy version: 28</blockquote>
<ul>
	<li>All processes and files have an SELinux security context which can be checked by ls -Z, e.g.</li>
</ul>
<blockquote>$ ls -Z
drwxr-xr-x. frank frank unconfined_u:object_r:user_home_t:s0 Desktop

unconfined_u:object_r:user_home_t:s0 is SELinux security context fields, unconfined_u means user, object_r represents role, user_home_t stands for (security context) type, s0 is mls which is not common used.</blockquote>
<ul>
	<li>By default, SELinux log messages are written to /var/log/audit/audit.log via the Linux Auditing System audited.  If the audit daemon is not running, then messages are written to /var/log/messages.  SELinux log messages are labeled with the 'AVC' keyword</li>
</ul>
There are some reasons why SELinux deny access :
<ul>
	<li>A mislabeled file</li>
	<li>A process running under the wrong SELinux security context</li>
	<li>A bug in policy . An application requires access to a file that wasn't anticipated when the policy was written and generate an error</li>
	<li>An intrusion attempt</li>
</ul>
The first three we can deal with.

Take apache accessing /svn/authz for example,
<blockquote>ps axZ | grep httpd
system_u:system_r:httpd_t:s0 5166 ? Ss 0:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0 5181 ? S 0:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0 5182 ? S 0:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0 5183 ? S 0:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0 5184 ? S 0:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0 5185 ? S 0:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0 5350 ? S 0:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0 5351 ? S 0:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0 5352 ? S 0:00 /usr/sbin/httpd -DFOREGROUND
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 5362 pts/0 S+ 0:00 grep --color=auto httpd</blockquote>
httpd_t is the selinux type of httpd.
<blockquote>cd /svn
ls -Z
-rw-r--r--. root root unconfined_u:object_r:default_t:s0 authz</blockquote>
default_t is the type of /svn/authz file.

For SELinux, access is only allowed between similar types, so apache running as httpd can access type httpd_sys_content,  but  cannot read /svn/authz because the directory and file(s) have wrong security context even though user give 777 permission for it via DAC.

In order to let apache access this file, we need to set correct security context type 'httpd_sys_content_t' by following command:
<blockquote>sudo chcon -Rv --type=httpd_sys_content_t /svn</blockquote>
-R means recursive.
