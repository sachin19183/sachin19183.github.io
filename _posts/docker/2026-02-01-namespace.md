---
layout: single
title: Linux Namespace- The isolation engine
date: 2026-02-01
categories: [DOCKER]
tags: [Fundamentals]
---

> A process inside a namespace sees its own “version” of the system.
In other words, a namespace makes process think that it is alone on the system. It basically isolates system resources per process group.

**Mental Model**

Imagine 3 containers running on a host:


| Container | What it sees                    |
| --------- | ------------------------------- |
| A         | PID 1, own IP                   |
| B         | PID 1, different IP             |
| Host      | Real processes + all containers |


Each container believes:

> “I am the only system running.”

Namespaces create that illusion.

### ⚙️ Why Namespaces Were Needed

chroot isolated files, but processes could still:

- See all system processes
- Share network stack
- Interfere with IPC
- Access host users

Containers needed deeper isolation:

- Process trees
- Networking
- Hostname
- Users
- Mounts

Namespaces solved this. If chroot is:

> “You see a different folder”

Namespaces are:

> “You see a different world”

### 🧱 Types of Linux Namespaces (Container-Relevant)

There are several, but these 6 matter most:


| Namespace | Isolates       |  Conatiner effect                  |
|-----------|----------------|------------------------------------|
| PID       | Process ID     | Container has its own process tree |
| NET       | Networking     | Own IP, Port, Interface            |
| MNT       | Mount points   | Own filesystem view                |
| UTS       | Hostname       | Container hostname                 |
| IPC       | Shared Memory  | Isolated messaging                 |
| USER      | UID/GID mapping| root inside != root outside        |



---

### 1️⃣ PID Namespace — Process Isolation

Isolates process IDs.

Inside container:
```bash
ps aux
```

you might see:
```bash
PID 1  python app.py
PID 7  worker.py
```
but on host:
```bash
PID 4321 python app.py
PID 4328 worker.py
```

**Key Effects**

- Container has its own process tree
- PID 1 exists inside container
- Killing PID 1 stops container

This is why entrypoint matters in Docker.

---

### 2️⃣ NET Namespace — Network Isolation

Each namespace gets its own:
- IP address
- Interfaces
- Routing table
- Ports

for example:
Container A:
```bash
IP: 172.17.0.2
Port 80
```

Container B:
```bash
IP: 172.17.0.3
Port 80
```

Both can run Nginx on port 80 without conflict.This is the foundation for:
- Docker networking
- Kubernetes pod networking

---

### 3️⃣ MNT Namespace — Mount / Filesystem Isolation

Controls what filesystems a process sees. Works with (but beyond) chroot.

Each container can have:
- Different mounts
- Different volumes
- Different root FS

Example

Container mounts:
```bash
/data → persistent volume
/logs → host path
```

---

### 4️⃣ UTS Namespace — Hostname Isolation

UTS = Unix Time Sharing (historic name).

Allows each container to have:
```bash
hostname
```

Example:

| Container | Hostname  |
| --------- | --------- |
| A         | llm-api   |
| B         | vector-db |

Useful for distributed systems + logging.

---

### 5️⃣ IPC Namespace — Interprocess Communication

IPC Isolates:
- Shared memory
- Semaphores
- Message queues

IPC prevents processes from different containers sharing IPC channels. It is very important for:
- Databases
- High-performance apps

---

### 6️⃣ USER Namespace — User Isolation

Maps user IDs inside container to different IDs on host.

Example:

|Inside container	   | On host       |
|--------------------|---------------|
| root (UID 0)       | UID 100000    |

So container root is not equal to the host root. This is a huge security improvement.

---

### How it works practically

Example:

```bash
lsns
PID   TYPE  PATH
4021  pid   /proc/4021/ns/pid
4021  net   /proc/4021/ns/net
```
Each container process belongs to multiple namespaces.

When we run nginx on docker
```bash
docker run nginx
```
Docker creates namespaces for that container. so nginx thinks :
- Its PID is 1
- It owns the port 80
- It has its own root Filesystem
- It has its own hostname

---

 ### Role in Kubernetes

Every Pod gets namespaces:
- Pod containers share NET namespace → same IP
- But separate PID namespaces (unless shared)
- Shared volumes via MNT namespace

This is why multi-container pods can talk via localhost.
