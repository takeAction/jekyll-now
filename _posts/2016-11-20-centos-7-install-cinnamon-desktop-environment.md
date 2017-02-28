---
layout: post
title: centos 7 install cinnamon desktop environment
date: 2016-11-20 19:30
author: 2freesky
comments: true
categories: [linux]
---
yum -y install epel-release

yum -y groupinstall "X Window system"

yum -y install lightdm

yum -y install cinnamon

yum install gnome-terminal

systemctl set-default graphical.target
