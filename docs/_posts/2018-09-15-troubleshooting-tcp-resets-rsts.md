---
layout: post
title: "Troubleshooting TCP Resets (RSTs)"
date: 2018-09-15 12:00:00 -0600
---

Inconsistent issues are by far the most difficult to track down. Network inconsistencies are particularly problematic because there can often be many different devices that must be looked into in order to identify the root cause. The following troubleshooting goes through a couple of steps. The first part is to start a tcpdump process that will record TCP RSTs. Then you can send a lot of HTTP requests. Below is the command to issue the tcpdump and fork the process to the background. However, the output will still be sent to the active terminal session because of the trailing `&`.

```bash
sudo tcpdump -i any -n -c 9999 -v 'tcp[tcpflags] & (tcp-rst) != 0 and host www.somesite.com' &
```

Below is the command to issue lots of HTTP requests. The important part to understand about the below command is to go through the TCP build up and tear down that happens during the HTTP request process.

```bash
for i in {1..10000}; do curl -ks https://www.somesite.com/robots.txt > /dev/null ; done
```

Below is an example of what a potential output could be.

```
17:16:56.916510 IP (tos 0x0, ttl 62, id 53247, offset 0, flags [none], proto TCP (6), length 40)
10.1.1.1.443 > 192.168.5.5.41015: Flags [R], cksum 0x56b8 (correct), seq 3221469453, win 4425, length 0
17:17:19.683782 IP (tos 0x0, ttl 252, id 59425, offset 0, flags [DF], proto TCP (6), length 101)
10.1.1.1.443 > 192.168.5.5.41015: Flags [R.], cksum 0x564b (correct), seq 3221469453:3221469514, ack 424160941, win 0, length 61 [RST+ BIG-IP: [0x2409a71:704] Flow e]
17:18:54.484701 IP (tos 0x0, ttl 62, id 53247, offset 0, flags [none], proto TCP (6), length 40)
10.1.1.1.443 > 192.168.5.5.41127: Flags [R], cksum 0x46f7 (correct), seq 4198665759, win 4425, length 0
```

While it may be unclear exactly why the TCP RSTs are happening this does provide a mechanism to reproduce TCP RSTs behaviors to investigate on other devices in the Network traffic flow. Below is documentation on how to troubleshoot TCP RSTs for the F5.

[https://support.f5.com/csp/article/K13223](https://support.f5.com/csp/article/K13223)

Happy troubleshooting!
