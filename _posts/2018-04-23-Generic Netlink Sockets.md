---
layout: post
title:  "Generic Netlink Sockets"
date:   23/04/2018
categories: jekyll update
author: Gilson Varghese
background: '/img/netlink.jpg'
---

Communication between user space applications and Linux kernel apps are usually clumsy, especially when kernel has to initiate a communication and multiple apps need to participate. Here is where the netlink sockets become handy. But they have one disadvantage: The number of netlink channels or ports (protocols) are limited to 32 and most of them are predefined for other purposes such as firewall. This limitation can be overcome using generic netlink sockets.
The diagram shown below elucidates a typical generic netlink communication architecture.

<center><img class="img-fluid" src="https://miro.medium.com/max/308/1*ZGPzNuP4JUiivf-6G4nf8A.png" alt="Demo Image">
<span class="caption text-muted">Generic Netlink Socket Communication Path</span></center>

The user application and kernel modules are connected through generic netlink controller, which acts as a moderator for all messages. All processes connect to the controller through NETLINK_GENERIC protocol (which is like a TCP or UDP port). Separate channels can be created for each process, using families. And each family may have various attributes and commands to distinguish between messages. Even though raw messages can be send as generic netlink payload, it’s recommended to use netlink attributes, which gives self documentation and validation (by setting attribute policies for the family) to payload.
Families are created using family name, which is in turn mapped to an auto generated ID in the generic netlink controller. The application must therefore resolve the family name to get the ID, before communicating with modules through a generic netlink channel. Multicast to different applications is also possible using generic netlink.
Steps for Generic Netlink Channel Creation
At User Space
Open a Datagram or RAW socket with AF_NETLINK as the family and NETLINK_GENERIC as the port.
Bind the socket to netlink address of the process.
Get the family ID from the generic netlink controller
Send or receive packets to or from kernel or other applications
At Kernel
Create family attributes and operations (commands and receive callbacks)
Create callbacks for receive and send netlink messages
Register family


Example Code
https://github.com/a-zaki/genl_ex/
