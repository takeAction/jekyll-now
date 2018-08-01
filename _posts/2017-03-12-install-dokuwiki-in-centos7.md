---
layout: post
title: Install Dokuwiki
categories: [Linux]
---

## What is dokuwiki
   As its name implies, it is a wiki which can be used in a company to share knowldege.  
   It's web-based and database is not required.
   
   Another is MediaWiki.
   
## System requirements:
- web server, e.g. apache, ngnix, not tomcat
- php >= 5.2
- php-gd

## Installation
1. install apache
   * `sudo yum install httpd openssl mod_ssl`
   * make it automatically start when your system start-up : `systemctl enable httpd`
   * if you need to change default port 80 to others, then `sudo vi /etc/httpd/conf/httpd.conf`, change 
   
     > Listen 80
     
     ```
     sudo firewall-cmd --zone=public --add-port=8090/tcp --permanent
     
     sudo firewall-cmd --reload
     
     restart httpd to make it take effect
     ```
2. install php
   * `sudo yum install php php-gd`
   * `sudo vi /etc/php.ini`, change it likes `expose_php = Off`
3. download dokuwiki
   * choose Wrap plugin and Upgrade plugin on download page
   * unzip it to /var/www/html
   * modify ownership with `sudo chown apache: -R /var/www/html/dokuwiki`
4. config SELinux
   * sudo yum install policycoreutils-python
   * sudo semanage permissive -a httpd_t
   
5. modify firewall if necessary
   
   Incoming traffic on port 80 maybe blocked by default. If so, we must create a permanent firewall rule to accept it.
   ```
   sudo firewall-cmd --permanent --zone=public --add-service=http 
   sudo firewall-cmd --reload
   ```
   
6. restart apache
   * sudo systemctl restart httpd
   
## Usage
- open localhost/dokuwiki/install.php in browser and config it then remove install.php
- some userful plugin should be added, for instance, add new page, tag, pagelist
