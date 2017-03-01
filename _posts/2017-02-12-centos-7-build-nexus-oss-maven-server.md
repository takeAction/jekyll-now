---
layout: post
title: centos 7 build nexus oss maven server
date: 2017-02-12 22:53
categories: [linux, maven]
comment: true
---
## Installation

1. install oracle idk 8 and configure JAVA_HOME, make it as default
2. download nexus oss 3.2

 ```
sudo tar xvzf nexus-3.2.0-01-unix.tar.gz -C /opt
sudo chmod -R 777 sonatype-work
```
3. to start it from terminal manually, just enter nexus installation home/bin, then run command
`./nexus run`
In this case, we need to press ctrl+c keys to stop it.
4. access it by : 
`http://localhost:8081`

## Make it as a service
In order to make it start automatically after server reboot, we need to make it as service.

It is a good practice to run that service as a specific user that has only required access rights rather than root user.

We can configure the repository manager to run as a service with init.d or systemd. Both are startup frameworks used in Linux-based systems such as Ubuntu and CentOS.

- add linux user for nexus
`sudo vi /opt/nexus/bin/nexus.rc`

make previous file looks like 
`run_as_user="linux user"`

- create link
`sudo ln -s /opt/nexus/bin/nexus /etc/init.d/nexus`

- config init.d
```
cd /etc/init.d
sudo chkconfig --add nexus
sudo chkconfig --levels 345 nexus on
sudo service nexus start
```
The second command adds nexus as a service to be started and stopped with the command. chkconfig manages the symbolic links in /etc/rc[0-6].d which control the services to be started and stopped when the operating system restarts or transitions between run-levels. The third command adds nexus to run-levels 3, 4, and 5. Then the service command starts the repository manager.

## Access it from other computers
```
sudo firewall-cmd --zone=public --permanent --add-service=http
sudo firewall-cmd --zone=public --add-port=8081/tcp --permanent
sudo firewall-cmd --reload
```
