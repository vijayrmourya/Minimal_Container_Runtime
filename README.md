# 🐧 Minimal Container Runtime – A Docker-Lite System Using Native Linux Tools

## 🧩 Problem Statement

This project aims to replicate the core behavior of container runtimes like Docker using only native Linux features, without relying on containerd, Podman, or Docker itself.

The primary goal is to demonstrate how **Linux namespaces**, **chroot environments**, **cgroups**, and **filesystem mounts** can be combined to simulate container isolation at the process, filesystem, and resource levels.

---

## ✅ Expected Behavior

When executing a command such as:

```bash
sudo ./mycontainer run /bin/bash
````

The following behaviors are expected:

* 🔒 The launched process runs in a **separate PID namespace**, isolated from the host’s process tree.
* 🧱 The process runs within a **chroot jail** pointing to a minimal root filesystem (e.g., Alpine or BusyBox).
* 🧠 The container has a custom **hostname**, isolated via the UTS namespace.
* 📊 **Resource limits** (CPU and memory) are optionally enforced using **cgroups v2**.
* 📂 A writable `/proc` is mounted inside the container to provide accurate process info.
* 🧹 Upon exit, the container’s resources are cleaned up — including mount points and cgroup assignments.

---

## ⏭️ Planned Enhancements

* Support for custom resource constraints via CLI flags (e.g., `--mem`, `--cpu`)
* Automated cleanup on container exit or failure
* Basic network namespace support
* Root filesystem bootstrapping (e.g., with `debootstrap` or unpacked Docker images)

# Index

- Concepts
  - [Processes](concepts/processes/process.md)
  - [PID](concepts/processes/pid.md)
