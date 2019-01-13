---
layout : post
title : Configure Git Server
comments: true
categories : vcs
---

This post describes how to configure git server on centos 7.

Suppose we have two pcs, one is centos 7, another is mac and we want to make the centos pc as the git server, mac is
client.

We call them **centos-server** and **mac-client**.

### Configuration

#### Install git on both pcs

  For **centos-server**, run following command:
  
  `sudo yum install git`
  
  and check the git version with command `git --version`
  
  For **mac-client**, download the installation package from [git](https://git-scm.com/)

#### Add git user

  Add a user called `git` on **centos-server**.
  
  ```
  sudo useradd git
  passwd git
  ```

#### Create ssh key

  Create ssh key on **mac-client** by command `ssh-keygen -t rsa` or `ssh-keygen -C "email@domain.com"`
  (In windows, use `Puttygen`).

  It will ask you to provide the location for storing the key, just hit `Enter` to use the default location. 
  
  For second question - pass phrase, just hit `Enter` for no passphrase.
  
  Then it generates two keys - a public key(ends with `.pub`) and a private key, the print message on command console looks
  like:
  
  > Your identification has been saved in /Users/frank/.ssh/id_rsa.
  > Your public key has been saved in /Users/frank/.ssh/id_rsa.pub.

  The public key will be uploaded to the **centos-server** for allowing ssh access from **mac-client** later.

#### Upload public ssh key
   Upload public ssh key to **centos-server** from **mac-client**:
   
   ```
   cat /Users/frank/.ssh/id_rsa.pub | ssh git@192.168.2.102 "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
   ```

   This will create `authorized_keys` under the `/home/git/.ssh` of **centos-server**.

#### Set up reporistory on server

  In **centos-server**, run following commands to set up a git repository:
  
  ```
  su git
  cd git 
  mkdir p1
  cd p1
  git init --bare
  ```
  
  Or ssh to **centos-server** on **mac-client**:
  
  ```
  ssh git@192.168.2.102
  mkdir -p /home/git/p1
  cd /home/git/p1
  git init --bare
  exit
  ```
  
  Run `exit` to quit ssh connection

#### Set up a repository on client

  In **mac-client**:
  
  ```
  cd /home
  mkdir gitRepo
  cd gitRepo
  mkdir p1
  cd p1
  git init --bare
  touch a.html
  git add .
  git commit -m 'initial commit'
  git remote add origin ssh://git@192.168.2.102/home/git/p1/p1.git
  git push origin master
  ```
  
 `git commit -m "message" -a`, `-a` option means commits for all files in the repo. 
 If you made changes to only one you can specify the name of that file instead of using `-a`.
   
   
 `push` means push these changes to the server so the work is accessible over the Internet and 
 you can collaborate with other team members.

#### Clone

  If we want to clone project from **centos-server** to **mac-client**, then in **mac-client**:
  
  ```
  cd /home/frank/gitRepo
  git clone ssh://git@192.168.2.102/home/git/p1/p1.git cf
  ```
  
  this will clone `p1` to `cf` folder

#### Update 

  If we want to updtate the repository of **mac-client** from **centos-server**, 
  then in the local repository of **mac-client**:
  
  `git pull origin master`
  
### GUI tools

  If you are looking for some GUI tools to manage changes on local machines, 
  you can use GUI tools such as `QGit` or `GitK` for Linux.

### Restrict git user to only perform git commands

  We can restrict `git` user of **centos-server** to only perform git commands:
  
  `which git-shell` will show the git-shell path, e.g. `/usr/bin/git-shell`.
  
  `nano /etc/passwd` will show something like `git:x:1001:1001::/home/git:/bin/bash`
  
  change it to `git:x:1001:1001::/home/git:/usr/bin/git-shell`
  
### Use git in eclipse
