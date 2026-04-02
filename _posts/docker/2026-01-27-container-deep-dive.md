---
title: "Container Deep Dive — Under the Hood of Modern Containers"
date: 2026-02-11
categories: [docker]
classess: wide
tags: [containers, linux, namespaces, cgroups]
excerpt: "Deep dive into how containers work internally, including namespaces, cgroups, and filesystem isolation."
---

Containers are not a single technology.

They are a **composition of multiple Linux kernel primitives** working together to create what we call *modern containers* today.

**In simple terms:**

Isolation (Namespaces) + Resource Control (cgroups) +  
Layered Images (Union Filesystems) + Security (seccomp + Capabilities) +  
Networking (veth + bridges) = **Containers**

This post dives into each building block.

---

## 🧱 1️⃣ Namespaces — The Isolation Engine

**Problem solved:**  
> “How do we make a process think it’s alone on the system?”

Linux Namespaces isolate system resources for a group of processes.

Each container gets its own “view” of the system.

### Types of namespaces

| Namespace | Isolates |
|-----------|----------|
| PID       | Process IDs |
| NET       | Network stack |
| MNT       | Filesystem mounts |
| UTS       | Hostname/domain |
| IPC       | Inter-process comms |
| USER      | User & group IDs |

**Impact:**  
Processes inside a container cannot see or interact with host processes.

From inside the container, PID 1 looks like the first process on Earth.

---

## ⚙️ 2️⃣ cgroups (Control Groups) — Resource Governance

Originally developed by Google engineers (notably **Paul Menage** and **Rohit Seth**).

**Problem solved:**  
> “How do we limit CPU/RAM for groups of processes?”

Without cgroups, a single container could consume the entire host.

### cgroups control

- CPU shares/quotas  
- Memory limits  
- Disk I/O  
- Network bandwidth  
- Process counts

**Example:**

```bash
docker run -m 512m --cpus=1 nginx
```
This ensures the container cannot exceed defined limits.This is the foundation of multi-tenant compute.

---

## 🧩 3️⃣ Union Filesystems — Layered Images

Containers rely on layered filesystems.

**Common implementations:**

- AUFS (early — Ubuntu/Canonical)
- OverlayFS (current standard)
- Btrfs / ZFS (optional)

**Problem solved:**

“How do we make images lightweight and reusable?”

**How layering works:**

Example image stack:
Base OS (Ubuntu) + Python runtime + ML libraries + Model code
Only changed layers are stored or transferred.

### 🤖 Why this matters for AI images

AI/LLM containers are massive because they include:

- CUDA
- PyTorch
- Transformers
- Model weights
- Layering reduces:
- Build time
- Push/Pull size
- Storage costs

Without union FS, AI container distribution would be painfully slow.

---

## 🔐 4️⃣ Capabilities — Fine-Grained Privileges
In traditional linux model, root user has unlimited power.
```bash
rm -rf *
```
executed from / can wipe out everything as a root user.Container needed a saftey net against such concerns. Linux Capabilities split root privileges into smaller units.

Examples:
- CAP_NET_ADMIN
- CAP_SYS_TIME
- CAP_SYS_BOOT

**Container impact:**

You can run as root but drop dangerous powers:

```bash
docker run --cap-drop=ALL --cap-add=NET_BIND_SERVICE nginx
```

This enables port binding without giving full root control.

**Key benefit:** Multi-tenant security hardening.

---

## 🛡️ 5️⃣ seccomp — Syscall Filtering

seccomp = Secure Computing Mode

It controls which system calls a container can execute.

**Why this matters:**

If compromised, a container could attempt:
- Kernel exploits
- Privilege escalation
- Host manipulation

seccomp blocks risky syscalls. Docker’s default profile blocks ~44 syscalls.
This dramatically reduces kernel attack surface.

---

## 🧬 6️⃣ SELinux & AppArmor — Mandatory Access Control

Heavily contributed by Red Hat and Canonical.They provide policy-based isolation beyond namespaces.
They control:
- Which files a container can access
- Which processes it can interact with
- What actions it can perform

**Defense-in-depth example**

Even if a container escapes filesystem isolation:
- SELinux policies can still block host access
- Prevent sensitive file reads
- Restrict device interaction

Security is enforced at the kernel policy layer.

---

##  🌐 7️⃣ Virtual Networking — veth, Bridges & iptables

Containers require isolated networking stacks.

Linux provides this using:
- veth pairs (virtual Ethernet cables)
- Linux bridges
- iptables NAT
- Network namespaces

**Traffic flow**
Container eth0
   ↓
veth pair
   ↓
Linux bridge
   ↓
Host NIC
   ↓
Internet

**This enables**

- Kubernetes Pod networking
- Service routing
- Overlay networks
- Network policies

Every container gets its own virtual NIC.

---

## 🧠 8️⃣ chroot — The Ancestor of Containers

Before namespaces existed, there was chroot (1979).

It changed the root filesystem for a process. But it lacked:

- Network isolation
- Process isolation
- Resource limits

### 🧭 Mapping Kernel Features to Kubernetes

| Kernel Primitive | Kubernetes Feature       |
| ---------------- | ------------------------ |
| Namespaces       | Pod isolation            |
| cgroups          | Resource requests/limits |
| Union FS         | Container images         |
| veth networking  | Pod networking           |
| seccomp          | Pod security policies    |
| Capabilities     | Security contexts        |

### 🤖 Why This Matters for AI / LLM Deployment
When deploying AI workloads on Kubernetes:

- GPU pods → cgroups + device plugins
- Model containers → namespaces
- Large images → union filesystems
- Multi-tenant inference → seccomp + SELinux
- Traffic routing → virtual networking

Modern AI infrastructure is built directly on Linux kernel constructs.

---

## 🏁 Closing Thoughts

Containers feel lightweight and magical at the surface. 
But underneath, they are a carefully engineered stack of:
- Isolation primitives
- Resource governors
- Filesystem innovations
- Security enforcement layers
- Virtual networking constructs
