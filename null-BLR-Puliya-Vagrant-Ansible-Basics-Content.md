# Getting started with Vagrant & Ansible 
### null Bangalore Puliya Session 




# Hour 1


## Getting Started Using Vagrant 


## Installation Pre-requisites
- Virtualbox 


## Install Virtualbox
https://www.virtualbox.org/wiki/Downloads


## Windows and OS X Users
- Download and install
- *In Windows you will have to manually add installation folder to the path*

    setx PATH %PATH%;path/to/vbox/folder

## Linux Users 
Download and just run this command 
    
    $ sudo dpkg -i virtualbox-filename.deb

Sometimes you may need to install the `dkms` package as well
    
    $ sudo apt-get install dkms


## Install Vagrant 
https://www.vagrantup.com/downloads.html


## Windows and OS X Users
- Download and install
- *In Windows you may need to logout and login again for it to be visible in the path*


## Linux Users 
Download and just run this command 

    $ sudo dpkg -i vagrant-filename.deb


## Is it working?
Let's see if `VirtualBox` is installed and working
    
    $ VBoxManage -v

Let's see if `Vagrant` is installed and working
    
    $ vagrant -v


### Well Done - Have a cup of coffee 
<iframe src="https://giphy.com/embed/3jVT4U5bilspG" width="345" height="480" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/3jVT4U5bilspG">via GIPHY</a></p>



## Let's create a Virtual Machine 
Creating a new Virtual Machine is a 3 step process
1. We *init*ialise a directory
2. We bring *up* the virtual machine


## I lied. There is no Step 3

<iframe src="https://giphy.com/embed/cvAvMOpm6G6hW" width="480" height="416" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/cvAvMOpm6G6hW">via GIPHY</a></p>


## Setup a project

    $ mkdir vagrant_getting_started 
    $ cd vagrant_getting_started 
    $ vagrant init ubuntu/trusty64

Note:
    mreil/ubuntu-trusty-mini


## NOTE : About disk size and storage
- By default all boxes that are downloaded to home dir .vagrant.d
- If you would rather not do this (takes a lot of space)

Export this environment variable 

    $ export VAGRANT_HOME=/path/with/more/disk/space

*Reference https://www.vagrantup.com/docs/other/environmental-variables.html*


## We are all set, but
- So far we have just initialised the directory to store our VM
- At this point, we will not try to change any value in `Vagrantfile` that got created


### View the contents of the file
<iframe src="https://giphy.com/embed/o5oLImoQgGsKY" width="480" height="264" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/o5oLImoQgGsKY">via GIPHY</a></p>


## Our first VM

    $ vagrant up

This will take time, output will look like

    Bringing machine 'default' up with 'virtualbox' provider...
    ==> default: Box 'ubuntu/trusty64' could not be found. Attempting to find and install...
        default: Box Provider: virtualbox
        default: Box Version: >= 0
    ==> default: Loading metadata for box 'ubuntu/trusty64'
        default: URL: https://vagrantcloud.com/ubuntu/trusty64
    ==> default: Adding box 'ubuntu/trusty64' (v20170918.0.1) for provider: virtualbox


## Yes, slow internet is slow
<iframe src="https://giphy.com/embed/o5oLImoQgGsKY" width="480" height="264" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/o5oLImoQgGsKY">via GIPHY</a></p>


## Is it working?

    $ vagrant ssh

If it works properly this is what we will see

    vagrant@vagrant-ubuntu-trusty-64:~$


## Lets review this

    $ vagrant init <provider name/box name>
    $ vagrant up
    $ vagrant ssh 

> In 3 commands (and decent Internet), we go from no VM to a full blown VM with SSH


## Stopping the VM

    $ vagrant halt

    ==> default: Attempting graceful shutdown of VM...


## Don't run this command but make a note

    $ vagrant destroy

This will delete the VM from your machine



## So why is Vagrant awesome?
> Vagrant is a tool for building and managing virtual machine environments in a single workflow


## Vagrant provides 
- easy to configure
- reproducible
- portable 

work environments


## Why should security folks use Vagrant?


## IMHO
1. Create different `Vagrantfiles` and use them to bring up VMs on various operating systems
2. Share these files with each other and stop wasting time in configuration and troubleshooting




## Diving into Vagrantfile
The big file that we had can be as simple as 

    Vagrant.configure("2") do |config|
        config.vm.box = "ubuntu/trusty64"
    end


## But Virtualbox has so many more options
Such as 
- Setting RAM
- Showing GUI 
- Choosing Network Bridge, HostOnly


## Setting options in Vagrantfile
- Try changing the RAM
- Try using the Private Network


## Lets set up 2 Machine Vagrantfile

    $ mkdir vagrant_two_machines
    $ cd vagrant_two_machines
    $ wget https://bit.ly/puliya-file -O Vagrantfile
    $ vagrant up
    $ vagrant status 


## SSHing into the machines
SSH into `web`

    $ vagrant ssh web

SSH into `db`

    $ vagrant ssh db




# Hour 2

## Getting Started Using Ansible 


## Installing Ansible
On Linux and OSX 

    $ sudo pip install ansible

On Windows 

    $ cd vagrant_getting_started
    $ vagrant up
    $ vagrant ssh
    $ sudo pip install ansible


## Getting our inventory ready

    [web]
    192.168.56.101

    [db]
    192.168.56.102


## Basic usage by using the ping module
All we want to see is, if we can execute a command in an automated manner against one of the instances that we started using vagrant earlier.


## For DB machine

    $ ansible db \ 
    --inventory-file=inventory \
    --module-name=ping \
    --user=vagrant \
    --private-key=.vagrant/machines/db/virtualbox/private_key


## For Web machine

    $ ansible web \ 
    --inventory-file=inventory \
    --module-name=ping \
    --user=vagrant \
    --private-key=.vagrant/machines/web/virtualbox/private_key


###Note
    1) Private key should have permissions to be only accessible by the user, i.e., chmod 0400 will help.
    2) inventory file should not have executable permission.
    3) Files in folders shared with Windows will get their executable permissions set.

## Using Ansible is awesome
- Define a way to install and configure software<!-- .element: class="fragment" data-fragment-index="1" -->
- One command can run against multiple computers<!-- .element: class="fragment" data-fragment-index="2" -->
- It uses SSH so nothing special required on the machine where commands need to be run<!-- .element: class="fragment" data-fragment-index="3" -->
- Integrates with Vagrant<!-- .element: class="fragment" data-fragment-index="4" -->




## Ansible Integration with Vagrant
Its as simple as this snippet in our `Vagrantfile`

    config.vm.provision "ansible" do |ansible|
        ansible.playbook = "playbook.yml"
    end


## Running this 
Two scenarios 

If we are starting a new vm, provisioning will kick in

    $ vagrant up 

If the machine is already running

    $ vagrant provsion


![Ansible output from vagrant provisioning](http://res.cloudinary.com/makash/image/upload/v1506098674/ansible-output-from-vagrant_zjsvej.png)




# Hour 3 and Hour 4


## Getting Productive with Vagrant 
Now that we know the basics, what can we do with this? 


## Using Boxes

    $ vagrant box add ubuntu/xenial64

- We have already seen this command in a different format
- In this case, the VM is not created. 

Just the VM disk is downloaded and kept ready for reuse later

    $ vagrant box list


## Sharing Boxes

    $ vagrant package 


## Using Vagrant Cloud
[Vagrant Cloud](https://app.vagrantup.com/boxes/search)



## Ansible Roles and Playbooks
Roles allow for variety of software to be installed by sharing of Ansible playbooks


## Ansible Galaxy
[Browse Ansible Galaxy](https://galaxy.ansible.com/intro)


## Installing DVWA

    $ mkdir vagrant_webgoat
    $ cd vagrant_webgoat/
    $ git clone https://github.com/appsecco/vulnerable-apps.git
    
    $ vagrant init ubuntu/trusty64
    


## Add this snippet to `Vagrantfile`
    config.vm.network "forwarded_port", guest: 80, host: 8001
    config.vm.provision "ansible" do |ansible|
        ansible.playbook = "vulnerable-apps/dvwa-ansible/dvwa/main.yml"
    end


## Bring up the VM

    $ vagrant up 


## Phansible
[http://phansible.com/](http://phansible.com/)




<iframe src="https://giphy.com/embed/hcpVSCSwDcKju" width="480" height="267" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/the-end-hcpVSCSwDcKju">via GIPHY</a></p>
