Get Instructure Canvas LMS up and running using Vagrant and Ansible.

## USAGE

To start up a single-host Canvas development environment, run:

    $ vagrant up

This will spin up the VM, install all dependencies, do all necessary prerequisites, and start the application, which you can then visit at http://localhost:3000.

To connect to the running VM, run:

    $ vagrant ssh

To shut down and completely clean up the VM, run:

    $ vagrant destroy

For all other management of the VM, refer to Vagrant's documentation.

Within the VM, the Canvas application is deployed to the `/apps/canvas` directory owned by the `canvas` user account. To switch to the `canvas` user, run:

    $ sudo -iu canvas


## PREREQUISITES

You must have Ansible, VirtualBox, and Vagrant installed on your workstation.

To set these up in Ubuntu 12.04 64-bit:

    $ sudo apt-get install python-pip
    $ sudo pip install ansible
    
    $ wget http://download.virtualbox.org/virtualbox/4.3.26/virtualbox-4.3_4.3.26-98988~Ubuntu~precise_amd64.deb
    $ sudo dpkg -i virtualbox-4.3_4.3.26-98988~Ubuntu~precise_amd64.deb
    
    $ wget https://dl.bintray.com/mitchellh/vagrant/vagrant_1.7.2_x86_64.deb
    $ sudo dpkg -i vagrant_1.7.2_x86_64.deb


## ABOUT CANVAS

For more about Instructure Canvas see: https://www.canvaslms.com

Canvas open-source repository: https://github.com/instructure/canvas-lms


## AUTHOR

David Adams, daveadams@gmail.com


## LICENSE

The software in this repository is public domain.
