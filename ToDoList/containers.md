## 🧱 What is a Container *Really* Made Of?

A container is **just a regular Linux process** —
but with **isolation** and **control** applied using native kernel features:

| Concept                  | Purpose in Containers                                                     |
| ------------------------ | ------------------------------------------------------------------------- |
| **Namespaces**           | Isolate what the process can "see" (e.g., processes, network, filesystem) |
| **Cgroups**              | Control how much CPU, memory, disk I/O a container can use                |
| **Chroot / pivot\_root** | Limit what files the process can access (its root `/`)                    |
| **Mounts**               | Give access to special filesystems like `/proc`, or overlay mount layers  |
| **Capabilities**         | Drop dangerous root privileges even if the process runs as root           |
| **Seccomp**              | Restrict which syscalls the process can use (security hardening)          |
| **OverlayFS (optional)** | Copy-on-write filesystem to avoid modifying base image                    |

These are **all Linux kernel features** — Docker, containerd, Podman, etc. are just **wrappers** around them.

---

## 🔍 Concepts You Need to Learn for This Project

Below is your concept checklist to go from scratch to full container understanding.

---

### 1️⃣ **Linux Namespaces** – *What the process can see*

| Namespace | Isolates                  | Command / Tool              |
| --------- | ------------------------- | --------------------------- |
| `pid`     | Process tree              | `unshare --pid`, `ps`       |
| `mnt`     | Mount points/filesystems  | `mount`, `unshare --mount`  |
| `uts`     | Hostname and domain       | `hostname`, `unshare --uts` |
| `net`     | Network interfaces/IPs    | `ip netns`, `unshare --net` |
| `ipc`     | Shared memory, semaphores | `unshare --ipc`             |
| `user`    | UID/GID mappings          | `unshare --user`            |

🧠 Learn: `unshare`, `clone`, `setns`, `/proc/[pid]/ns/`

---

### 2️⃣ **chroot / pivot\_root** – *Filesystem isolation*

* `chroot /path/to/rootfs` = restricts process to fake `/`
* `pivot_root` = more secure and flexible version used by Docker
* `/proc`, `/sys`, `/dev` must often be mounted manually inside the container

🧠 Learn: `chroot`, `pivot_root`, `mount`, `mount -t proc`, bind mounts

---

### 3️⃣ **Control Groups (Cgroups v2)** – *Resource limits*

| Cgroup Control | Example                                    |
| -------------- | ------------------------------------------ |
| Memory         | `memory.max = 100M`                        |
| CPU            | `cpu.max = 50000 100000` (50% of one core) |
| PIDs           | `pids.max = 50`                            |

🧠 Learn: `/sys/fs/cgroup/`, `cgroup.procs`, cgroups hierarchy

---

### 4️⃣ **Mounts and OverlayFS** – *Filesystems and copy-on-write*

* Mount new filesystems inside container (`/proc`, `/tmp`)
* OverlayFS allows base image + writable upper layer (like Docker’s union fs)

🧠 Learn: `mount`, `umount`, `mount --bind`, OverlayFS

---

### 5️⃣ **Capabilities** – *What root inside the container can do*

* By default, root in container can’t use dangerous syscalls (like `mknod`)
* Use `capsh`, `setpriv`, or drop caps via `prctl` or libcap

🧠 Learn: `capsh --drop`, `/proc/self/status`, `capsh --print`

---

### 6️⃣ **Seccomp** (Optional) – *Syscall filtering*

* Restrict container processes from calling risky syscalls
* Used for sandboxing untrusted code

🧠 Learn: `seccomp`, `libseccomp`, `prctl(PR_SET_SECCOMP)`

---

## 🧭 Summary: The 6 Things You Must Know to Build a Container

| #   | Area               | Purpose                         | Command to Learn                |
| --- | ------------------ | ------------------------------- | ------------------------------- |
| 1️⃣ | Namespaces         | Visibility isolation            | `unshare`, `clone`, `setns`     |
| 2️⃣ | chroot/pivot\_root | Filesystem isolation            | `chroot`, `pivot_root`, `mount` |
| 3️⃣ | Cgroups            | Resource control                | `/sys/fs/cgroup/*`              |
| 4️⃣ | Mounts             | Access to `/proc`, `/dev`, etc. | `mount`, `bind`, `tmpfs`        |
| 5️⃣ | Capabilities       | Safe privilege control          | `capsh`, `setpriv`, `getcap`    |
| 6️⃣ | Seccomp (bonus)    | Syscall restriction             | `seccomp`, `prctl`, `auditd`    |

---
