---
title: Docker handson- The ops perspective
date: 2026-02-28
categories: [docker]
classes: wide
tags: [containers, linux, namespaces, cgroups]
excerpt: "Docker handson commands used in day to day work arranged in a logical sequence"
---

1. Check docker version
   ```bash
       docker version
   ```

2. Check for existing docker images
   ```bash
       docker images
   ```

3. Copy new image onto your **"docker host"**
   ```bash
       docker pull <image-name>
   ```

4. Start a container from the pulled image
   ```bash
       docker run --name <container-name> -d -p 8080:80 <image-name>
   ```
   -d to start container in background
   -p 8080:80  Map port 80 inside container with port 8080 inside docker host

5. Check which containers are running
   ```bash
       docker ps
   ```

6. Execute a command inside the container
   ```bash
       docker exec -it <container-name bash
   ```
   Here we can run a limited set of linux command only as most containers only ship with essential apps and tools to keep them small.

7. Exit from container shell
   ```bash
      exit
   ```
8. Delete the container and image(cleanup)
   ```bash
       docker stop <container-name>
       docker ps -a
       docker rm <container-name>
       docker ps -a
   ```
   The -a option of the ps command shows the container entry after stopping it. The same entry disappears once you remove it using the docker rm command.


This is a typical sequence of commands executed in a docker container lifecycle from an operational engineer's perspective

