---
title: 'An Introduction to Containers: Part 1'
tags:
  - containers
url: 1935.html
id: 1935
categories:
  - How To
date: 2018-12-03 07:07:58
---

Containers have been around since the 1970's and over the last 5 years, there has been a big push in the software development world to really start implementing them.  In this multi-part series, I'll look at what containers are as well as how they are used and applied.  The first part of the series is a simple, high level overview. One of the biggest benefits to using containers is that they allow your software more portable.  Instead of requiring your software to run on a machine that has a specific operating system and maybe even specific software that your program relies on, you can package all of its dependencies together (including the operating system and any other software it relies on) together in a container or a set of containers.

### Virtual Machines

Prior to containers being popular, we used (and continue to use) virtual machines (VMs).  A virtual machine is an isolated operating system that sits within a normal host operating system.  They make it seem like you are running separate computers (with potentially different operating systems) on the same machine.  They are created with software emulation and/or hardware virtualizations and because they are on the same machine, the different operating systems share the physical hardware resources.  This is managed with a hypervisor, which sits between the hardware and the virtual machines. However, because they need a full copy of an operating system they can take up lots of resources.

### Containers

Containers solve many of the same things that VMs solve.  However, instead of virtualizaing the entire computer, containers just virtualize the OS.  Containers share, via read-only access, the kernel and usually the binaries and libraries.  This read-only access significantly reduces resources because the container does not need to reproduce them. Unlike VMs which each have their own OS, containers share their host OS resources.

### Benefits

*   Inherently Cross-Platform
    *   Because it is self-contained, containers allow you to build your application on a Windows machine and run it on a Linux machine (or vice versa).  The developer does not need to create multiple versions of the same code for the different operating systems, it will work on every device.
*   Quicker Spin Up Time
    *   Containers tend to be only megabytes in size and can start up in just seconds, where an equivalent VM could take minutes to start and are many times larger.
*    Microservices
    *   Instead of having one huge application, the application can be broken up into many 'microservices'.  Think of microservices as functions.  You can break your application up into smaller parts, which allows multiple teams to work independently (as long as they don't change the way the microservices interact with each other) and allows you to test easier and faster -- much like functions.

### What are Docker and Kubernetes?

Docker is an open-source software that allows you to build and run containers.  It was developed primarily for Linux but it can be integrated into various tools like AWS, Microsoft Azure, and Kubernetes. Kubernetes is an open-source container-orchestration system which allows your containers to be deployed, scaled, and to communicate with each other. In a later post, we will dive deeper into how to use containers and container software such as Docker, Kubernetes, Swarm, and Packer.  

_Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._