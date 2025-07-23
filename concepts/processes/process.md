# 🧠 What is a Linux Process?

* A **Linux process** is an instance of a running **program** or **command**. Even a simple command like `ls` is executed as a separate process.
* For example, if you run the `ls` command in **three different terminals** at the same time, the system will create **three separate processes**, each with its own unique **Process ID (PID)**.
* Suppose you have a shell script with 5 commands and run it in one terminal — that instance becomes a process in memory. If you then **modify the script** to include 3 more commands (total 8), and execute it in another terminal, the system creates a **new, independent process** for that updated version. Both versions can run concurrently, each as a separate process with their own PID.

# Program vs. Process: 

A program is a static set of instructions (an executable file on disk). 
A process is the dynamic, active instance of that program running in memory. 
You can run the same program multiple times, and each execution will be a separate process with its own unique characteristics.

# Key Notes:

* systemd (PID 1) is the ancestor of all processes on a Linux system.
* Your terminal (like gnome-terminal or xterm) spawns a shell process (e.g., bash) — this becomes the parent for any command you run.
* Each time you run a command (ls, script, etc.), the shell forks a new child process with its own PID, and the shell becomes its PPID.
* Even different versions of the same script are treated as separate processes.

# Virtual Address Space: 

Each process believes it has access to the entire memory space, but this is an illusion created by the operating system. 
The kernel maps these virtual addresses to physical RAM, providing memory isolation between processes. 
One process cannot directly access the memory of another.

# Resources: 

Processes require resources to operate. These include:
- CPU Time: The process needs to be scheduled by the kernel to run on the CPU.
- Memory: For its code, data, stack, and heap.
- File Descriptors: References to open files, pipes, sockets, etc.
- Network Sockets: For network communication.
- Other Kernel Objects: Such as semaphores, mutexes, etc.

# Further Reads:

## Execution Context: 
This includes all the information the CPU needs to run the process, such as:
- Program Counter (PC): Points to the next instruction to be executed.
- CPU Registers: Store temporary data and control information.
- Stack: Used for function calls and local variables.

## Process ID (PID): 
Every running process on a Linux system is assigned a unique positive integer known as its Process ID (PID). 
This PID identifies the process to the kernel and other processes. You can see PIDs using commands like ps or top.
