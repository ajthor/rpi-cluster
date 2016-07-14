# Raspberry Pi Omega

_"To infinity and beyond!"_


## Table of Contents
- [About](#about)
- [Packages](#packages)
- [Usage](#usage)
- [Description](#description)
- [Future](#future)

## About
Raspberry Pi Omega is a project to create a simple Raspberry Pi cluster to use as a home micro-server, a development platform, or for any number of other possible applications. 

_The big boys get big toys. Why can't we have our own?_

This project does assume you have familiarity with Linux, Raspian, Docker, and the other technologies used within.

## Packages

Raspberry Pi Omega consists of several repositories for managing and maintaining your cluster:
- __[rpiomega-bootstrap](https://github.com/ajthor/rpiomega-bootstrap)__ - Handles the setup for nodes in the cluster.
- __[rpiomega-salt](https://github.com/ajthor/rpiomega-salt)__ - Provisioner for the cluster. 
- __rpiomega-backup__ (coming soon) - Scheduled backups for files in the cluster.

---

## Usage
To set up a Raspberry Pi Omega cluster, make sure you install _Raspbian_ on the sd cards you want to use in your cluster. In the future, the _bootstrap_ and _salt_ scripts may cater to other operating systems; but for now, _Raspbian Jessie Lite_ is the easiest to use with the cluster. You can find a download for Raspbian at [Raspberry Pi's Website](https://www.raspberrypi.org/downloads/). 

Once you have Raspbian flashed to the sd cards, insert the sd cards into the boards and power them up. Using the IP address assigned to the device, copy the ssh key from your local machine to the device and run the bootstrap script. See the [rpiomega-bootstrap](https://github.com/ajthor/rpiomega-bootstrap) repo for more details. 

```
ansible-playbook site.yml -i "< current IP address >," --extra-vars "ip_address=< target static IP address> hostname=rpiomega-node-< node ID > type=node"
```

Once the bootstrap script has completed, you will need to add the salt minion's key to the salt master. See the [rpiomega-salt](https://github.com/ajthor/rpiomega-salt) repo for more details. From there, run `salt '*' state.apply` to provision the nodes. 

The nice thing about this process is that it can take less than 15 minutes to set up a cluster of multiple nodes.

## Description
The process that each of the scripts go through is outlined below:

### rpiomega-bootstrap
- Expand the root file system on the Raspberry Pi. Normally, this is handled using the `raspi-config` script. We use that in headless mode to do preliminary, basic setup steps for the Raspberry Pi.
- Update networking configuration files using pre-defined templates. Here, we set up the static IP address of the device and connect it to the network. There is absolutely no checking done with this step, so it is important to know exactly which IP address you want to use to avoid conflicts. 
- Update the device's hostname. This allows us to reach the device by a more human-friendly name.

### rpiomega-salt
- Install common development packages, including `git` and `python`. 
- Install [Backblaze's B2 Cloud Storage](https://www.backblaze.com/b2/cloud-storage.html) cli on each node. This is a top-notch cloud storage service offered by a great company. Plus, you get 10GB free. We will use this for _rpiomega-backup_ when it is created. 
- Install Docker on every machine, using the updated version, rather than the one provided by default. 
- Update this script from the GitHub repository on the master node. 
- Install Consul on the master and start it as a docker image. 
- Install Docker Swarm on every machine in the cluster. 

---

## Future
Some things I would like to see happen in the future:
- Extensibility to other SBCs such as ODROID-C2, and connectivity with AWS and Google Cloud Services. 
- Bulk script for bootstrapping nodes. 
