---
layout: post
title: "Remote Wireshark and tcpdump"
date: 2018-09-15 12:29:00 -0600
---

This may come to a surprise to many people, but sometimes computers do not talk to each other the correctly. Luckily, packets don't lie. We can easily find out which computer is not communicating properly using either tcpdump and/or Wireshark. Below are by far the 2 most useful network analysis commands that I use.

## Print only the HTTP header information

The following command is useful when you only need to look at the HTTP headers, provided you are analyzing cleartext HTTP traffic.

```bash
sudo tcpdump -i any -A -s 10240 '(port 80) and (((ip[2:2] - ((ip[0]&0xf)<<2)) - ((tcp[12]&0xf0)>>2)) != 0)' and not host 127.0.0.1 | egrep --line-buffered "^........(GET |HTTP\/|POST |HEAD )|^[A-Za-z0-9-]+: " | sed -r 's/^........(GET |HTTP\/|POST |HEAD )/\n\1/g'
```

## Wireshark to a remote host

For more in-depth protocol analysis, it may be necessary to leverage Wireshark. The command below is super useful to pipe the tcpdump output from a remote machine to your local instantiation of Wireshark. This way you don't have to take a capture, save it locally, and then open up Wireshark. Below is the command that is needed.

```bash
ssh ubuntu@ -p 22 -i ~/sshpemkeyauth.key "sudo tcpdump -s 0 -U -n -w - -i any not port 22" | wireshark -k -i - &
```

You can make it into a bash function like I have below as well.

```bash
function wiresh {
  ssh ubuntu@$1 -p 22 -i ~/sshpemkeyauth.key "sudo tcpdump -s 0 -U -n -w - -i any not port 22" | wireshark -k -i - &
}
```

This way you only have to do the following at the command line to take a remote wireshark capture:

```bash
wiresh <IP address>
```

I hope this helps anyone else out there. I have to give a shout out to StackOverflow for inspiring this post. BC
