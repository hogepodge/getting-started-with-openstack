# Getting Started with OpenStack 
A little project to quickly set up all-in-one OpenStack using Vagrant

## Introduction
The Vagrantfile provided in this project will download and launch a CentOS 7 Vagrant Box,
install RDO, and provide you an environment to follow along with in the original
[Getting Started With OpenStack](https://www.openstack.org/videos/austin-2016/getting-started-with-openstack)
video. These are the same (updated) materials used to present that session.

## Requirements
Your machine must meet a minimum set of requirements to successfully
run an all-in-one OpenStack installation. At a minimum you need:

1. A compatible hypervisor.
  * Linux: KVM, Virtual Box.
  * OS X: VMware Fusion, Virtual Box.
  * Windows: VMWare Workstation, Virtual Box.
2. Vagrant to control your hypervisor, along with supported provider.
3. At least 8 GB of RAM to support a 6 GB virtual machine.
  * 12 GB or more to support an 8 GB virtual machine is highly recommended.

## Installing OpenStack

1. Install Vagrant from: https://www.vagrantup.com/docs/installation/
2. Download this repository: `git clone https://github.com/hogepodge/getting-started-with-openstack`
3. Start the virtual machine and install OpenStack: `$ cd getting-started-with-openstack; vagrant up`
4. Wait for the machine startup and installation to complete, it will take some time.

## Pausing the Installation

To suspend your installation you can use: `$ vagrant halt`

To resume it:`$ vagrant up`

## Troubleshooting

If you have trouble, please file a issue or pull request and we'll help you
out as quickly as possible.

If your vagrant environment fails for any reason, you can start off with
a clean slate by destroying the VM: `$vagrant destroy -f`

Then beginning the process again with `$vagrant up`.

## Acknowledgments

Thanks to Dan Radez for the initial content, and to both him and
Kenneth Hui for the introductory presentation at the Austin OpenStack 
Summit.
