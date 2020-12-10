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

## Considerations

Due to time constraints during development of this project I did not get a chance to expand the project to include a DB/dynamic web application which would've provided a more useful example of my skills in this area.

## Improvements

### Security

One improvement that could be made is setting up SSL for the load balancer, we could integrate a service like letsencrypt to generate a cert, or use openssl for self-signed CA for a test domain. This would mean expanding ansible to deal with domains in the proxy role.

### Functionality

Ideally I would have liked to have deployed a test application using python flask to setup a dummy application that is a little more impressive than displaying hello world in HTML but I had a time constraint regarding this.

Better yet setting up and hosting a simple django application would've been nice as it would've allowed me to expand this to include a PostgresQL database, this would require additions to Vagrantfile for the VM and also a new DB role to be configured.

# Roles

## Common

Runs apt-get update on new instances and deploys some packages needed for DNS, although this can be ignored as I was just testing an idea for the loadbalancer to use DNS to find upstream servers in order to make it more dynamic.

## Application

Deploys nginx to an isntance with required configuration to display helloworld

## Proxy

Deploys nginx as a loadbalancer with required configuration. In loadbalancer.conf template we are getting the IP's of the instances in the application group which we then use to populate the upstream server endpoints in the configuration file. This is dealt with dynamically so that increasing the 'NUMBER_OF_WORKERS' parameter will automatically be accounted for in configs.