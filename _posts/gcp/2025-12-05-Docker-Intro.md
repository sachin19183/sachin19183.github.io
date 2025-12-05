---
layout: single
title: Introduction to Dockers
date: 2025-12-05
categories: [DOCKER, Fundamentals]
---

With this post , we begin to explore cloud envrironment. When i first started studying clouds, it seemed very hard and complex because of so many interdependent things which we need to learn and understand before we can begin with the actual cloud. The base for it is virtualization, Dockers and containers. 
I would recommend to explore on linux commands, basic architecture, firewalls, subnets, ip addresses as well to feel more comforatable when you actually start on clouds (be it any vendor)
Coming back to the focus of this post, lets start with virtualization

## VIRTUALIZATION

Virtualization is a technology that allows you to create multiple simulated environments (like computers or servers) on a single physical machine.

**Simple idea: One physical computer → many virtual computers running inside it.**

It divides hardware resources (CPU, RAM, storage) so multiple systems can work independently on the same machine.
Think of virtualization like having multiple rooms inside one big house. 
Each room has its own door, bed, and furniture — completely separate from others, even though they share the same house.

## VIRTUAL MACHINE

A Virtual Machine (VM) is a software-based computer that runs on top of a physical machine using virtualization technology.
Each VM includes:
-Its own full operating system (Windows, Linux, etc.)
-Virtualized CPU, memory, and storage
-Applications running inside it

**Simple idea: A VM acts like a real computer inside another computer.**

VMs are managed by a software layer called a Hypervisor(e.g., VMware, VirtualBox, Hyper-V)
So we can draw the inference that VM virtualizes the hardware.It comes with certain drawbacks :
- It is resource heavy (CPU,RAM, storage)
- Startup time is a concern for those applications which need to be highly available
- The image size is large and it is not easy to transfer them on a different physical server

Later on when we see these in the context of cloud computing we will relaize that these are significant blockers.

To overcome these , we use another technique called containerization. While virtual machines virtualize hardware, containerization virtualizes the operating system — making application deployment faster and more efficient.
Containerization is a technology that allows applications to run in isolated environments called containers.
Each container includes everything the app needs — like code, libraries, and dependencies — so it can run consistently anywhere.

**Simple idea:Containerization packages the app and its environment together, so it runs the same on any system.**

Key Features of containerization:
- Lightweight as containers share the host OS kernel
- Fast start/boot time
- Easy to port to other OS/machines/cloud infra
  
Containers are like shipping containers
You can load any type of goods inside, seal it, and transport it globally. No one cares what’s inside — every port can handle it the same way. Containers provide standard packaging and consistent transport, just like app containers provide consistent deployment.

**Containerization = Package once → Run anywhere ✔**

## DOCKER

Docker is an open source centralized platform designed to create, deploy & run applications.
Docker uses container on the host Operating system (OS) to run applications to use the same linux kernel as a system on the host computer, rather than creating a whole virtual OS.

Before the docker era, many developers faced the problem that a particular code is running in their local system (dev environment) but when they deploy things in production, the application breaks down. This was probably because their were dependencies/ libraries missing in production which were present in dev and the developers were unaware about it.
