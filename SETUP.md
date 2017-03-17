# Setting up the Raspberry Pis

Setting up the Raspberry Pi nodes is pretty straightforward. Follow the instructions on [Raspberry Pi's Website](https://www.raspberrypi.org/documentation/) to download and install the latest version of Raspbian Jessie Lite.

After you flash the SD card, you will need to perform three steps to get the Raspberry Pi ready for use.

## 1. Enable SSH

To enable SSH, you will need to open up a terminal and navigate to the SD Card directory. For Mac, it is usually located at `/Volumes/boot`. Once there, run the command `touch ssh`. This will create a blank file on the SD Card that tells the Pi to enable SSH the next time it boots.

```
$ cd /Volumes/boot
$ touch ssh
```

## 2. Configure Networking

If you are going to connect your Raspberry Pi via ethernet, you can skip this step. To set up WiFi, you will need to create a file on the SD Card called `wpa_supplicant.conf`.

First, you will need to generate a secure passphrase so that you don't use your actual WiFi password in your configuration files. You can do this using a tool such as `wpa_passphrase`. Be sure to change <my_ssid> to the SSID of your network. If you use `wpa_passphrase`, you can copy and paste the output directly into the `wpa_supplicant.conf` file. More thorough instructions can be found [here](https://www.raspberrypi.org/documentation/configuration/wireless/wireless-cli.md).

To edit the `wpa_supplicant.conf` file, we can use nano, a built-in editor that comes standard on most machines.

```
$ wpa_passphrase <my_ssid> <password>
$ sudo nano wpa_supplicant.conf
```

To exit the editor, press 'Ctrl-X' and be sure to select 'yes' to save your changes.

Your `wpa_supplicant.conf` file should look something like this:

```
country=US
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
  ssid="<my_ssid>"
  psk=131e1e221f6e06e3911a2d11ff2fac9182665c004de85300f9cac208a6a80531
}
```

## 2.1 (Optional) Set a Static IP

It can be a good idea to set a static IP, especially for the Master. When Docker creates a swarm, it advertises the address of the manager using the IP address assigned to the node. Making sure the IP address doesn't change in-between startup and shutdown of your cluster means that you won't have to create a new swarm each time you reboot.

To give your device a static IP, edit `/etc/dhcpcd.conf` to include the following lines:

```
interface eth0
static ip_address=192.168.0.10/24
static routers=192.168.0.1
static domain_name_servers=192.168.0.1 8.8.8.8
```

Be sure to change the interface if you are using WiFi. In most cases, the WiFi interface will be something like `wlan0`. Also, it can help to make the IP address something large, like 192.168.0.100, so that you can be sure the address won't be occupied by another device on the network.

## 3. Change the Hostname

Lastly, we need to change the host name of the device. Insert the SD Card into the Raspberry Pi and boot it up. This can take up to 90 seconds. Once you have done so, SSH into the node using the hostname of the Raspberry Pi. Usually, this is going to be `raspberrypi`. If you have multiple Raspberry Pis connected to your network at the same time, you may have to connect to them using their IP address.

```
$ ssh pi@raspberrypi.local
```

The default password is `raspberry`.

Now, we need to change the hostname of the Pi. The easiest way to achieve this is using raspi-config and following the on-screen instructions to change it.

```
$ sudo raspi-config
```

For the 'master', use `raspiomega-master`, and for each 'node', use `raspiomega-node-<#>`, where the '<#>' is replaced by a unique number for each node.

From this point on, each subsequent node can be set up entirely using Salt. However, you will need to manually set up the Master using the instructions below.
