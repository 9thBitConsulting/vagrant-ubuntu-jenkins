# vagrant-ubuntu-jenkins

## Description

Vagrant Virtualbox build to install a Jenkins CI Server on Ubuntu 16.04. Includes Ansible
provisioning scripts.

## Requirements

- Virtualbox
- Vagrant
- Ansible

## Usage

### Install Ansible dependencies

    $> ansible-galaxy install -r requirements.yml

### Optional 

Some optional Vagrant plugins may be installed to ease the usage and creation
of these images.

- **vagrant-cachier** - Will provide local caching of package for Linux dsitributions
avoiding the need to re-download packages every time.

- **vagrant-dns** - Manages DNS name entries for your Vagrant boxes using macOS's 
resolver mechanism

### Startup

    $> vagrant up
