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

This project assumes you have familiarity with Linux, Raspian, Docker, and the other technologies used within.

## What is Raspberry Pi Omega?

Raspberry Pi Omega is an easily expandable cluster computing platform using the credit-card sized SBC, Raspberry Pi. It is designed as an educational and personal tool for software development, and

Raspberry Pi Omega is developed as a platform for performing development and computing tasks on a cluster of Raspberry Pi's. It is designed to be an inexpensive, educational entry-point into cluster computing, utilizing technologies such as Configuration Management and Provisioning, Container-Based Virtualization, and Continuous Integration.

Though the system can run with a single node, it is better to use at least 3 nodes. With enough Raspberry Pi's, the system can become quite powerful.

See more in the rpiomega docs.

### Why "Omega"?

In set notation, Omega is the greek letter used to represent the Universal Set, and is also used to represent the first number after infinity. The cluster is designed to work as a set of nearly any number of nodes, hence the name.

## Repositories

Raspberry Pi Omega consists of several repositories for managing and maintaining your cluster:
- __[rpiomega-salt](https://github.com/ajthor/rpiomega-salt)__ - Provisioner for the cluster. Also handles starting the Docker swarm.
- __rpiomega-backup__ (coming soon) - Scheduled backups for files in the cluster.

---

## Usage

To set up a Raspberry Pi Omega cluster, make sure you install _Raspbian_ on the sd cards you want to use in your cluster. In the future, the _salt_ scripts may cater to other operating systems; but for now, _Raspbian Jessie Lite_ is the easiest to use with the cluster. You can find a download for Raspbian at [Raspberry Pi's Website](https://www.raspberrypi.org/downloads/).

Once you have Raspbian flashed to the sd cards, insert the sd cards into the boards and power them up. Using the IP address assigned to the device, copy the ssh key from your local machine to the device and run the bootstrap script. See the [rpiomega-salt](https://github.com/ajthor/rpiomega-salt) repo for more details.

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
