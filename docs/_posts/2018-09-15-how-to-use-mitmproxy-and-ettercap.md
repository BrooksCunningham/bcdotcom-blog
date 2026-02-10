---
layout: post
title: "How to use Mitmproxy and Ettercap together on OS X El Capitan"
date: 2018-09-15 12:00:00 -0600
---

## Abstract

The purpose of this document is to provide guidance on how to configure both of the tools mitmproxy and ettercap to work together to monitor mobile application traffic. This document is intended for educational purposes. Using the techniques here with malicious intent may result in criminal consequences. Before going any further, I want to point out one of the better quotes that I have seen in a man file. Below can be found in the man file of ettercap.

> "Programming today is a race between software engineers striving to build bigger and better idiot-proof programs, and the Universe trying to produce bigger and better idiots. So far, the Universe is winning." - Rich Cook

## Install ettercap

Homebrew is amazing. Ettercap is as easy to install as issuing the following command.

```bash
brew install ettercap
```

## Install mitmproxy

The docs for mitmproxy are fairly straightforward. Mitmproxy is a python package that runs on Python 2.7. The link below has the official documentation. [http://docs.mitmproxy.org/en/latest/install.html#installation-on-mac-os-x](http://docs.mitmproxy.org/en/latest/install.html#installation-on-mac-os-x)

## Configure Port Forwarding

First enable IP forwarding. This is outlined in the transparent proxy guide in the following link: [http://docs.mitmproxy.org/en/latest/transparent/osx.html](http://docs.mitmproxy.org/en/latest/transparent/osx.html).

```bash
sudo sysctl -w net.inet.ip.forwarding=1
```

Brian John does an excellent job explaining the new port configuration that needs to occur for OS X Mountain Lion. See the link below for his guide: [http://blog.brianjohn.com/forwarding-ports-in-os-x-el-capitan.html](http://blog.brianjohn.com/forwarding-ports-in-os-x-el-capitan.html). I will go through the steps necessary for mitmproxy to work as expected based on the information that Brian John provided.

### Create the anchor file

`/etc/pf.anchors/mitm.pf`

Add the following lines to the anchor file, `mitm.pf`.

```
rdr pass on en0 inet proto tcp from any to any port 80 -> 127.0.0.1 port 8080

rdr pass on en0 inet proto tcp from any to any port 443 -> 127.0.0.1 port 8080
```

### Create the pfctl config file

`/etc/pf-mitm.conf`

Add the following lines to the pfctl config file.

```
rdr-anchor "forwarding" load anchor "forwarding" from "/etc/pf.anchors/mitm.pf"
```

### Enable or Disable Port Forwarding

To activate or deactivate port forwarding, use one of the following commands.

**Enable:**

```bash
sudo pfctl -ef /etc/pf-mitm.conf
```

**Disable:**

```bash
sudo pfctl -df /etc/pf-mitm.conf
```

## Combining the tools

Now that port forwarding is configured, fire up mitmproxy with the following command.

```bash
python2.7 mitmproxy -T --host
```

mitmproxy will by default listen for incoming HTTP and HTTPS traffic on the proxy port 8080. Next, use the following command to start ARP spoofing the target device.

```bash
sudo ettercap -T -M arp:remote ///80,443/ ////
```

The final command should look something like the following.

```bash
sudo ettercap -T -M arp:remote /192.168.0.1//80,443/ /192.168.1.54///
```

You will need to trust the mitmproxy CA if you would like to inspect HTTPS traffic. The steps for this configuration can be found in the following link: [http://docs.mitmproxy.org/en/latest/certinstall.html](http://docs.mitmproxy.org/en/latest/certinstall.html). Once mitmproxy and ettercap are both running, then you should start seeing network traffic from your mobile device on your OS X device. Good Luck with inspecting traffic! Let us know in the comments below if you have any questions or feedback on this article. Brooks
