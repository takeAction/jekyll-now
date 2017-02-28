---
layout: post
title: symbolic link and hard link
date: 2017-01-02 22:52
author: 2freesky
comments: true
categories: [linux]
---
Symbolic link or called soft link, is a file which points to another file or directory, just like a pointer.

It does not contain data of target file, while hard link does.

Soft link is able to link to file/directory of remote computer and hard just link file on the same filesystem.

If we delete or move the target file, soft link becomes unusable, whereas hard link keeps the content of that file.

create soft link command:


[code]ln -s target_file soft_link_file[/code]


we can do operations on soft_link_file, e.g. cp, rm, just as we can do on target_file.

create hard link:


[code]ln target_file hard_link_file[/code]

