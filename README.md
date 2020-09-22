# Quick Start

This guide is meant for building lab environments with a dedicated controller node and a compute node.

## Prerequisites Linux

You need to have a system with a fresh install of Linux. I recommend you to use  ubuntu 16.04 for it has been most tested.

At the first we create two VMs via VirtualBox or other virtualization software. And each of them need the following hardware configuration. 

devstack-controller node: 

4G RAM + 80G HD

NIC 1# Bridged Adapter

NIC 2# Internal Network

NIC 3# Host-only Adapter



devstack-compute node: 

2G RAM + 40G HD

NIC 1# Bridged Adapter

NIC 2# Internal Network

## Network Configuration

Configure each node with a static IP. For Ubuntu edit `/etc/network/interfaces`:

```
# The primary network interface

auto enp0s3
iface enp0s3 inet static
address 192.168.31.10
netmask 255.255.255.0
gateway 192.168.31.1
dns-nameservers 192.168.31.1

auto enp0s8
iface enp0s8 inet manual
 
auto enp0s9
iface enp0s9 inet manual
```

Change the NIC's name according to your virtual machine. The above one is for controller node, here is another one for compute:

```
# The primary network interface
auto enp0s3
iface enp0s3 inet static
address 192.168.31.11
netmask 255.255.255.0
gateway 192.168.31.1
dns-nameservers 192.168.31.1

auto enp0s8
iface enp0s8 inet manual
```

We only allocate two NICs to compute node as it doesn't connect with external network. Use the command `systemctl restart network.service` or `reboot` your linux to renew these configurations.

You will need to replace the ubuntu original apt sources.list with aliyun's or other's  so as to speed the apt installation process. After you backup and edit the `/etc/apt/sources.list` , remeber to run `sudo apt update` to update your system.

## Installation shake and bake

### Add Stack User

DevStack should be run as a non-root user with sudo enabled, you can create a separate stack user to run DevStack with:

```
$ sudo useradd -s /bin/bash -d /opt/stack -m stack
```

Since this user will be making many changes to your system, it should have sudo privileges:

```
$ echo "stack ALL=(ALL) NOPASSWD: ALL" | sudo tee /etc/sudoers.d/stack
$ sudo su - stack
```

### pip configuration

Use `pip` command to check if pip have installed properly. If not, use `sudo apt install python-pip` to install it. 

Since we have a slow speed using default pip source, we can change it to douban source. To to this, follow

 these steps:

1. make a new directory in stack's home directory: `mkdir ~/.pip`

2. add a configuration file: `vi ~/.pip/pip.conf`

The contents of `pip.conf` have included in this repo.

### Download DevStack

```
$ git clone https://github.com/openstack/devstack -b stable/ocata
$ cd devstack
```

Download devstack repo through git and use `-b` to pull a dedicated openstack version branch. 

Up to this point all of the steps apply to each node in the cluster. From here on there are some differences between the controller node and the compute node.

### Configure Controller Node

The controller node runs all OpenStack services. Configure the controller’s DevStack in `local.conf` which included in this repo.

### Configure Compute Node

The compute node only run the OpenStack worker services. Create a `local.conf` with contents given in this repo.

### Start the install

```
$ ./stack.sh
```

Be care, you should run devstack scripts  in controller node first or you will end with some fault. 

This will take a 60 - 90 minutes, largely depending on the speed of your internet connection. Many git trees and packages will be installed during this process.

You now have a working DevStack! Congrats!