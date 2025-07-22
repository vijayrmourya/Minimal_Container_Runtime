# What is a Namespace in Linux?

### **Definition:**

A **namespace** in Linux is a kernel-level feature that provides **process-level isolation** for system resources.

> It allows processes to have **their own independent view** of certain system aspects — such as the file system, process tree, network stack, etc.

You can think of namespaces as **"virtualized lenses"** — each process can only "see" what the namespace allows it to see.

---

## 🧠 Why Namespaces Matter (Real-World Use)

They form the core of **containers**, **sandboxing**, and **multi-tenant environments** by:

* Hiding other users and processes
* Preventing network conflicts
* Providing isolated root filesystems
* Assigning different hostnames
* Restricting IPC and memory sharing

When you run a Docker container — it's Linux namespaces doing the real magic behind the scenes.

Even if you're not using Docker, or Podman — your process still lives inside default namespaces created at boot

---

## 📚 Types of Namespaces in Linux

Here’s a table summarizing all major namespace types:

| Namespace | Isolates                  | CLI Tool                    | Description                                                     |
| --------- | ------------------------- | --------------------------- | --------------------------------------------------------------- |
| `pid`     | Process IDs               | `ps`, `unshare --pid`       | A process in this namespace sees only its own process tree      |
| `mnt`     | Mount points              | `mount`, `unshare --mount`  | Separate mount tables (e.g., different root filesystems)        |
| `uts`     | Hostname/domain           | `hostname`, `unshare --uts` | Allows a process to have its own hostname                       |
| `net`     | Network stack             | `ip`, `unshare --net`       | Separate interfaces, routing tables, IPs, etc.                  |
| `ipc`     | Shared memory, semaphores | `ipcs`, `unshare --ipc`     | Isolates System V IPC and POSIX message queues                  |
| `user`    | UID/GID mappings          | `id`, `unshare --user`      | Allows process to have its own view of user IDs                 |
| `cgroup`  | Cgroup hierarchy          | `ls /sys/fs/cgroup/`        | Isolates access to control group hierarchies (since Linux 4.6+) |
| `time`    | Boot and monotonic time   | Linux 5.6+                  | Experimental: lets processes control their own time view        |
  
✅ Every process belongs to a namespace.
❌ But not every process runs in an isolated namespace.

---

## 🧪 Real Example: PID Namespace

```bash
sudo unshare --pid --fork --mount-proc bash
```

* Creates a new shell where:

  * PID 1 is `bash`
  * `ps aux` will only show the current shell and its children
  * `/proc` is remounted to reflect the new PID namespace

This is exactly how a container gets a clean process tree.

---

## 🧠 How Namespaces Work (Kernel View)

Every Linux process has a **`task_struct`**, which tracks:

* What namespaces it's part of (via namespace pointers)
* Each type of namespace is independent
* You can join or leave namespaces using `unshare()`, `clone()`, or `setns()`

For example:

* `clone(CLONE_NEWNET)` → create a new network namespace
* `setns(fd, CLONE_NEWUTS)` → join a namespace from another process

---

## 📦 Combining Namespaces (Like Docker Does)

A true container combines several namespaces:

```bash
sudo unshare --pid --uts --mount --ipc --net --user bash
```

* You get:

  * New process tree
  * New hostname
  * New mount table
  * Isolated memory queues
  * New network interfaces
  * Custom UID mapping

This is what **Docker** or **Podman** actually do behind the scenes.

---

## 🔒 Security Use Cases

* `user` namespaces: allow **non-root users** to run "root" inside a container (but it's fake root)
* `net` namespaces: isolate apps from the host network
* `pid` + `mount`: restrict what a process can see or kill

---

## 📂 Where Are Namespaces in the File System?

You can explore namespaces using `/proc`:

```bash
ls -l /proc/$$/ns/
```

Output:

```
ipc -> ipc:[4026531839]
mnt -> mnt:[4026531840]
net -> net:[4026531992]
pid -> pid:[4026531836]
user -> user:[4026531837]
uts -> uts:[4026531838]
```

Each is a symlink to a kernel namespace inode.

---

## ✅ TL;DR Summary

| Term      | Meaning                                                                 |
| --------- | ----------------------------------------------------------------------- |
| Namespace | A way to isolate system resources per process                           |
| Goal      | Give each process (or group of processes) its own "virtual environment" |
| Used in   | Containers, sandboxes, chroot jails, Linux security                     |
| Key tools | `unshare`, `setns`, `clone`, `/proc/[pid]/ns/`                          |

---

Would you like a **namespace-by-namespace practical demo script** to run and explore each one visually?
