# Automation.HardenedLinux

## Summary

This is the offical base AMI image that will be used across all teams.

A new AMI can be created from [this Teamcity project](https://build.provinnovate.com/project.html?projectId=SecDevOps_AutomationHardenedLinuxImage&tab=projectOverview).

## Base community AMI or ISO.

The base community AMI is hard coded into group_vars/aws.yml file. The image was selected from the [official Centos site](https://wiki.centos.org/Cloud/AWS). When updating select the one for Us-west-2

When running the script against workstation VM use the latest [CentOS minimal ISO](https://www.centos.org/download/).

## How to run scripts.

### Running locally against workstation VM.

***Note: Using ubuntu for this example***

1. Install required packages
```bash
$apt-get install -y python-dev libssl-dev libffi-dev libxml2-dev libxslt-dev python-pip
```
2. Install ansible
```bash
$sudo pip install ansible
```
3. Create local hosts file with target machine IP.

```
[hoststargets]
192.168.124.173
```
4. Run the ansible script.
```
$ansible-playbook configuretarget.yml -i hosts --extra-vars "targetenv=local sshconfiguser=root"
```
***Note: Setting targetenv just disables the custom sshd configuration from being copied over, this allows your existing root user to still SSH to the VM***

### Running within AWS on teamcity agent.

```
$ su teamcity
$ cd ~
$ export WORKON_HOME=~/venvs
$ source /usr/bin/virtualenvwrapper.sh
$ rmvirtualenv ansible
$ mkvirtualenv ansible
$ pip install -r requirements.txt
$ workon ansible
$ ansible-playbook awstarget.yml
```
## How to run scripts from mac to vm.

### Running locally against workstation VM created by vagrant.

### Run following commands on mac os

1. Install vagrant and virtual box
```bash
brew cask install virtualbox
brew cask install vagrant
brew cask install vagrant-manager
vagrant init elastic/centos-7-x86_64; vagrant up --provider virtualbox
```
2. Install ansible
```bash
$sudo pip install ansible
```
3. Create local hosts file with target machine IP.

```
[hoststargets]
centos.local  ansible_port=2222 ansible_host=127.0.0.1  ansible_user=vagrant
```
4. copy id_rsa.pub content under ~/.ssh folder and append it to vm ~/.ssh/authorized_keys files(which allows ansible to run via ssh)
5. Run the ansible script.
```
$ansible-playbook configuretarget.yml -i hosts --extra-vars "targetenv=local sshconfiguser=root"
```
***Note: Setting targetenv just disables the custom sshd configuration from being copied over, this allows your existing root user to still SSH to the VM***
