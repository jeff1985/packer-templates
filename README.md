# packer-templates

[Packer](https://www.packer.io/) templates to build Ubuntu LTS Virtualbox images with root ssh login, 
that can be used as starting point for VPS used in production. 

This repository is based on kaorimatz/packer-templates. The main idea was to take vagrant packer 
script and remove insecure vagrant keys from it. I also removed all unneeded files as i wanted to 
focus on ubuntu images.

## Usage

Clone the repository:

    $ git clone https://github.com/jeff1985/packer-templates && cd packer-templates

Build a machine image from the template in the repository:

    $ packer build -only=virtualbox-iso -var 'ssh_password=XXX' ubuntu-14.04_amd64.json
    
I recommend to create a separate yaml file containing ssh_password and run it like this:
   
    $ packer build -only=virtualbox-iso -var-file=../packer.passwords.yml ubuntu-14.04_amd64.json 


## Configuration

You can configure each template to match your requirements by setting the following [user variables](https://packer.io/docs/templates/user-variables.html).

 User Variable       | Default Value | Description
---------------------|---------------|----------------------------------------------------------------------------------------
 `compression_level` | 6             | [Documentation](https://packer.io/docs/post-processors/vagrant.html#compression_level)
 `cpus`              | 1             | Number of CPUs
 `disk_size`         | 40000         | [Documentation](https://packer.io/docs/builders/virtualbox-iso.html#disk_size)
 `headless`          | 0             | [Documentation](https://packer.io/docs/builders/virtualbox-iso.html#headless)
 `memory`            | 512           | Memory size in MB
 `mirror`            |               | A URL of the mirror where the ISO image is available
 `ssh_password`      | xxx           | The ssh password for the root user to be set.
 
### Example

Build an uncompressed Arch Linux vagrant box with a 4GB hard disk using the VirtualBox provider:

    $ packer build -only=virtualbox-iso -var compression_level=0 -var disk_size=4000 ubuntu-14.04_amd64.json

## Security note

It is common for VPS boxes to have ssh root login enabled in their initial state. Make sure to enable ssh key auth when you start using the server:

    $ ssh-copy-id root@vps
    $ ssh "root@vps" "sed -i 's/PermitRootLogin.*/PermitRootLogin no/g' /etc/ssh/sshd_config; service ssh restart"