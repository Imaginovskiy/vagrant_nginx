# Introduction

This is a project which will deploy a number of nginx webservers determined by the 'NUMBER_OF_WORKERS' parameter. These are also connected to a load balancer running nginx.

I decided to use Ansible as the confguration manager due to native support in Vagrant as well as familiarity with Ansible

Once the machines are created we will then create an ansible inventory based on the groups defined in the Vagrantfile (Line 58 - 61)

We will then begin the process of bootstrapping, due to limitations in vagrant we run ansible on the last created instance with a limit of 'all', this allows us access to the ansible facts from other hosts aiding in the bootstrapping of the loadbalancer instance.

We group the instances into roles so there are application / common / proxy groups which could be further extended. These groups are handled in a parameterised manner also controlled by 'NUMBER_OF_WORKERS' parameter.

## Requirements 

This project requires a vagrant install and a working virtualbox environment

Steps for this can be found here: 

```
https://linuxize.com/post/how-to-install-vagrant-on-ubuntu-20-04/
```

A working ansible installation is also needed to aid with bootstrapping this can be achieved as follows;

```
sudo apt install ansible
```

## Deployment

This is quite a simple deployment to work with as all config is dealt with by ansible the only commands needed are:

```
vagrant up
```

Once created the loadbalancer will be available at http://172.19.120.10 serving from the application instances.