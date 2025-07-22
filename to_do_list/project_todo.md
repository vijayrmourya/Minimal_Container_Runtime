# ✅ **Project TODO List – Minimal Container Runtime (Docker-Lite)**

---

## 🧱 PHASE 1: Setup & Planning

### 📝 1. Create Project Structure

* [ ] Create root folder: `minimal-container-runtime`
* [ ] Inside it, create the following directories:

  ```
  minimal-container-runtime/
  ├── rootfs/               # Holds extracted minimal Linux filesystem
  ├── scripts/              # Utility scripts (e.g., build-rootfs.sh)
  ├── logs/                 # Runtime logs (stdout/stderr of container)
  ├── cgroups/              # Cgroup files (optional)
  ├── mycontainer.sh        # Main executable
  └── README.md             # Documentation
  ```
* [ ] Initialize `git`, `.gitignore`, and `README.md`

---

## 📦 PHASE 2: Create a Minimal Root Filesystem

### 🛠 2. Build the Container Filesystem (`rootfs/`)

Choose one of:

#### Option A: Use `debootstrap` (Ubuntu)

* [ ] Install: `sudo apt install debootstrap`
* [ ] Run:

  ```bash
  sudo debootstrap --variant=minbase jammy ./rootfs http://archive.ubuntu.com/ubuntu/
  ```

#### Option B: Use BusyBox (Tiny)

* [ ] Download and extract BusyBox static build
* [ ] Create `/bin`, `/etc`, `/proc`, `/sys`, `/dev` inside `rootfs/`
* [ ] Copy `busybox` binary and symlink essential commands:

  ```bash
  ln -s busybox ls
  ln -s busybox sh
  ```

---

## 🧪 PHASE 3: Isolate a Process using `unshare`

### 🔄 3. Implement Namespaces and Isolation

* [ ] In `mycontainer.sh`, use `unshare` to:

  * [ ] Create a new PID namespace
  * [ ] Create a new UTS namespace (for hostname)
  * [ ] Create a new mount namespace

* [ ] Command:

  ```bash
  sudo unshare --fork --pid --mount --uts bash
  ```

* [ ] From the child process:

  * [ ] Set custom hostname: `hostname mycontainer`
  * [ ] Isolate mounts: `mount --make-rprivate /`

---

## 🧱 PHASE 4: Isolate Filesystem using `chroot`

### 📂 4. Change Root and Mount Essentials

* [ ] Use `chroot ./rootfs /bin/bash` to enter new root
* [ ] Inside rootfs:

  * [ ] Mount `/proc`:

    ```bash
    mount -t proc proc /proc
    ```
  * [ ] Ensure `/dev/null`, `/dev/zero`, etc. exist (optional for compatibility)

---

## 📊 PHASE 5: Add Resource Limits using Cgroups v2

### 🧮 5. Apply Memory, CPU, and PIDs Limits

#### Setup Cgroups

* [ ] Create folder: `/sys/fs/cgroup/mycontainer/`
* [ ] Assign current process to the cgroup:

  ```bash
  echo $$ > /sys/fs/cgroup/mycontainer/cgroup.procs
  ```

#### Apply Limits

* [ ] Limit CPU:

  ```bash
  echo 50000 100000 > /sys/fs/cgroup/mycontainer/cpu.max  # 50% CPU
  ```

* [ ] Limit Memory:

  ```bash
  echo 100M > /sys/fs/cgroup/mycontainer/memory.max
  ```

* [ ] Limit PIDs:

  ```bash
  echo 50 > /sys/fs/cgroup/mycontainer/pids.max
  ```

* [ ] Add cleanup logic to remove cgroup folder on container exit

---

## 🛠️ PHASE 6: Build `mycontainer run` CLI

### ⚙️ 6. Create CLI Functionality

* [ ] Parse arguments: `./mycontainer.sh run /bin/bash`
* [ ] Optional flags:

  * [ ] `--mem=100M`
  * [ ] `--cpu=25%`
  * [ ] `--hostname=mycontainer`
* [ ] Create usage/help menu if no args provided

---

## 🧹 PHASE 7: Add Cleanup and Logging

### 🔚 7. Handle Process Exit & Clean Resources

* [ ] Use `trap` to capture SIGINT / SIGTERM
* [ ] Unmount `/proc`
* [ ] Remove cgroup entries
* [ ] Log container stdout/stderr to `logs/container_<timestamp>.log`

---

## 🔐 PHASE 8: Add Optional Security Features

* [ ] Use `capsh` or `setpriv` to drop root capabilities
* [ ] Set up read-only mount with `mount -o ro`
* [ ] Restrict syscalls using `seccomp` (optional advanced)

---

## 🧪 PHASE 9: Test & Validate

* [ ] Launch basic shell: `./mycontainer.sh run /bin/bash`
* [ ] Run `ps aux` → should only show process tree from within
* [ ] Run `hostname` → should show container-specific name
* [ ] Verify resource limits: memory usage, CPU throttle
* [ ] Try exceeding limits — container should crash/fail gracefully

---

## 📝 PHASE 10: Documentation and GitHub Polish

* [ ] Add full usage examples in `README.md`
* [ ] Add ASCII architecture diagram or flowchart
* [ ] Add demo GIF or screenshot
* [ ] Commit changes regularly with meaningful messages
* [ ] Push to GitHub with appropriate tags/releases

---

## 🌟 BONUS / STRETCH GOALS (Optional)

* [ ] Use `pivot_root` instead of `chroot` for better root switch
* [ ] Add overlayfs support for copy-on-write
* [ ] Build a simple image loader from `.tar` rootfs
* [ ] Add network namespace and veth pair setup
* [ ] Add a basic JSON config support (like Docker's run args)

---
