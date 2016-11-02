OULibraries vagrant_infrastructure
=========

A starting point for testing infrastructure playbooks in Vagrant.  Provisions multiple machines, including an Ansible control machine.  Then uses the Ansible machine to configure the various boxes appropriately for their roles.  Currently provisions:
* Drupal
* CAS
* Nginx

Though there is a way to go before all of these provision nicely together, since the CAS and Nginx roles are still being written.

Requirements
------------

* Requires [Vagrant](https://www.vagrantup.com/downloads.html). 
* Creates an Ansible control VM, so you don't need a working Ansible install.

Installation
------------

1. Install Vagrant.
1. Clone this repo to a local folder.
1. Configure the project to build:

      On Linux or MacOS, you can symlink your project in projects to `project`, eg.
      ```
      # Linux or MacOS
      ln -s projects/example project
      ```
     
      On Windows, you will need to edit two files: `Vagrantfile` and `ansible.cfg`
      
      In `Vagrantfile`, specify your project
      ```
      -VAGRANTFILE_PROJECT="project"
      +VAGRANTFILE_PROJECT="projects/web"
      ```
     
      In the `ansible.cfg`, specify the path to your projects inventory
      ```
      -inventory = /vagrant/project/inventory.py
      +inventory = /vagrant/projects/web/inventory.py
      ```
1. Set an editor in your shell environment, eg.

      ```
      export EDITOR=vim
      ```
      
Notes
------------

* This thing currently expects all or nothing up and provision commands. Doesn't do useful port forwarding currently.
* Additional configuration is required for vagrant to forward to a privileged port. It's probably easier to use `ngrok` if you need something like that. 
    
Vagrant Usage 
------------

The following vagrant commands are likely to see the most use:

* `vagrant up` to start your vms. The box will build itself on first startup.
* `vagrant ssh $vm` to log in to `$vm`
* `vagrant halt` to shut down your vms
* `vagrant reload` bounces your vms. 

It maybe be neccessary to do a `halt` or `reload` if the guest VM gets
confused about its network, or loses its fileshares. This most
frequently happens when the host machine goes to sleep and/or moves
between networks.

Less frequently, you'll may want to reprovision to get the lastest
changes, or rebuild your VM Completely. In that case, you'll need
these commands:

* `vagrant provision` will re-run the ansible provisioners
* `vagrant destroy` to delete the VM, in case you want to start over

TODO
------------

* Add support for the rest of our environments
* Lots and lots. 