---
layout: post
comments: true
title: Get started at MVP Club session August 8, 2018
description: "Starting at the MVP Club, you will get to learn about development and cloud technologies."
date:   2018-08-08 23:30:00 +0530
author: hansen
featured: true
published: true
categories: [ jcu, mvpclub ]
permalink: firstmvpclubsession.html
image: https://i.imgur.com/LY2nOaF.jpg
---


### Agenda

- Introduction to the JCU MVP Club
- Virtualization
- Linux commands
- Vagrant and Ubuntu
- web server installation (nginx)


---
### Introduction to the JCU MVP Club
<h1 align="center">
JCU MVP Club
</h1>

- Why the MVP Club
- What are we doing
- How will we succeed

---
<h1 align="center">
JCU MVP Club
</h1>
<h3 align="center">
Why
</h3>

- Students need to expand IT knowledge
- Learn how to obtain additional skills
- Industry need skilled graduates

---
<h1 align="center">
JCU MVP Club
</h1>
<h3 align="center">
Mission
</h3>

- Improve IT and technology knowledge
- Bring motivation across subjects
- Improve the JCU Brand and graduates reputation

---
<h1 align="center">
JCU MVP Club
</h1>
<h3 align="center">
What
</h3>

- Learn by project solving
- Produce MVP (Minimum Viable Products)
- Work across subjects and institutes

_Keywords: **MVP**, **DevOps**, **Entrepreneurship**, **Business plan**, **Cloud**, **Development**_

---
<h1 align="center">
JCU MVP Club
</h1>
<h3 align="center">
How
</h3>

- Members will learn DevOps and development.
- Take in MVP projects from JCU research or startups.
- Operate across all JCU campuses

---

<h1 align="center">
Virtualisation
</h1>

### Virtualbox

Virtualbox is a virtualization engine from Oracle.
The platform is free to use and you can install various OS on your system.
We will be using Vurtualbox to run ubuntu and to use with Vagrant.

Download virtualbox from
https://www.virtualbox.org/wiki/Downloads

![ubuntu](https://assets.ubuntu.com/v1/29985a98-ubuntu-logo32.png)
Download ISO images from for ex. Ubuntu:
https://www.ubuntu.com/download/desktop

---

### Vagrant

Once you have install ubuntu. go ahead and install vagrant.

If you are running a mac, you can install directly on the mac.

We have heard of issues for some windows users, so please do go through the following steps on ubuntu:

1. Launch Ubuntu
### Check linux version
Start a terminal and run
```bash
$ (lsb_release -cs)
```

This command will print the Ubuntu codename. For example if you have Ubuntu version 18.04 the command will print **bionic**

2. Install virtualbox on Ubuntu

### Import VirtualBox GPG keys
Import the GPG keys of the Oracle VirtualBox repository to our system using the following commands:
``` bash
$ wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo apt-key add -
$ wget -q https://www.virtualbox.org/download/oracle_vbox.asc -O- | sudo apt-key add -
```
Both commands should output OK, indicating the keys are successfully imported and packages from this repository will be considered trusted.

### Add the VirtualBox repository
add the VirtualBox repository with the add-apt-repository command:

```bash
$ sudo add-apt-repository "deb http://download.virtualbox.org/virtualbox/debian $(lsb_release -cs) contrib"
```
### Install VirtualBox
Update the apt package list and install the latest version of VirtualBox with:
``` bash
sudo apt update
sudo apt-get install virtualbox
```

3. Install vagrant on Ubuntu

Open a terminal on Ubuntu and start the installation of Vagrant:

```bash
$ sudo apt-get install vagrant
```

### Verify Vagrant version
once vagrant is installed, you can verify it by following command:

```bash
$ vagrant
```
You will see the following response from vagrant.

```bash
$ vagrant
Usage: vagrant [options] <command> [<args>]

    -v, --version                    Print the version and exit.
    -h, --help                       Print this help.

Common commands:
     box             manages boxes: installation, removal, etc.
     connect         connect to a remotely shared Vagrant environment
     destroy         stops and deletes all traces of the vagrant machine
     global-status   outputs status Vagrant environments for this user
     halt            stops the vagrant machine
     help            shows the help for a subcommand
     init            initializes a new Vagrant environment by creating a Vagrantfile
     list            
     login           log in to HashiCorp's Vagrant Cloud
     package         packages a running vagrant environment into a box
     plugin          manages plugins: install, uninstall, update, etc.
     port            displays information about guest port mappings
     powershell      connects to machine via powershell remoting
     provision       provisions the vagrant machine
     push            deploys code in this environment to a configured destination
     rdp             connects to machine via RDP
     reload          restarts vagrant machine, loads new Vagrantfile configuration
     resume          resume a suspended vagrant machine
     share           share your Vagrant environment with anyone in the world
     snapshot        manages snapshots: saving, restoring, etc.
     ssh             connects to machine via SSH
     ssh-config      outputs OpenSSH valid configuration to connect to the machine
     status          outputs status of the vagrant machine
     suspend         suspends the machine
     up              starts and provisions the vagrant environment
     validate        validates the Vagrantfile
     version         prints current and latest Vagrant version

For help on any individual command run `vagrant COMMAND -h`

Additional subcommands are available, but are either more advanced
or not commonly used. To see all subcommands, run the command
`vagrant list-commands`.
```
### create your vagrant project
Create a folder for your project and start by adding your vagrant box:
```bash
$ vagrant box add ubuntu/trusty64
```

The output will look something like this:
```bash
==> box: Loading metadata for box 'ubuntu/trusty64'
    box: URL: https://vagrantcloud.com/ubuntu/trusty64
==> box: Adding box 'ubuntu/trusty64' (v20170619.0.0) for provider: virtualbox
    box: Downloading: https://app.vagrantup.com/ubuntu/boxes/trusty64/versions/20170619.0.0/providers/virtualbox.box
==> box: Successfully added box 'ubuntu/trusty64' (v20170619.0.0) for 'virtualbox'!
```
Initialize the vagrant box using the following command:
```bash
$ vagrant init ubuntu/trusty64
```

The output will look like this:

```bash
`Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.
```

Now you can startup your vagrant image:

```bash
vagrant up
```

This is what you will see on the terminal:
```bash
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Importing base box 'ubuntu/trusty64'...

...

==> default: Machine booted and ready!
```

Now you can connect to the vagrant ubuntu server running on your machine via ssh

```bash
$ vagrant ssh
```
Maybe your terminal will look like this once you are inside the ubuntu server:

```bash
Welcome to Ubuntu 14.04.5 LTS (GNU/Linux 3.13.0-133-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

 System information disabled due to load higher than 1.0

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

New release '16.04.3 LTS' available.
Run 'do-release-upgrade' to upgrade to it.


Last login: Wed Oct 18 21:08:02 2017 from 10.0.2.2
vagrant@vagrant-ubuntu-trusty-64:~$
```
### ssh issues
If the vagrant ssh command does not work then use the following command to know your ssh config

```bash
$ vagrant ssh-config
```
or create your ssh key using

```bash
$ ssh-keygen -t rsa
```

Type enter for default filename and location ~/.ssh
and set your password, then press enter to generate.
The output will look something like this:

```bash
$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/michael/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/michael/.ssh/id_rsa.
Your public key has been saved in /home/michael/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:Up6KjbnEV4Hgfo75YM393QdQsK3Z0aTNBz0DoirrW+c michael@ubuntu
The key's randomart image is:
+---[RSA 2048]----+
|    .      ..oo..|
|   . . .  . .o.X.|
|    . . o.  ..+ B|
|   .   o.o  .+ ..|
|    ..o.S   o..  |
|   . %o=      .  |
|    @.B...     . |
|   o.=. o. . .  .|
|    .oo  E. . .. |
+----[SHA256]-----+
$
```
Read more about ssh-keygen [here](https://www.ssh.com/ssh/keygen/)

### Linux command
Please follow this [link](https://www.computerworld.com/article/2598082/linux/linux-linux-command-line-cheat-sheet.html) to get a list of linux commands

And here is a[tutorial](http://www.linuxcommand.org/lc3_learning_the_shell.php) to learn the linux commandline
