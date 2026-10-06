

Extended xv6-riscv Kernel: Operating Systems Project
Student Name: Javeria Shahid
Roll No: BCS-F24-M48
Course: Operating Systems
Instructor: Sir Raza
Overview
This project extends the xv6-riscv teaching kernel (a re-implementation of Unix Version 6 for RISC-V, written in C) provided as the base by the instructor. The goal is to add ten advanced features covering memory management, scheduling, concurrency, IPC, file systems, and security.
Base kernel: mit-pdos/xv6-riscv (instructor-provided version)
 Target platform: RISC-V (QEMU riscv64)
Planned Features
Memory Management
1. Lazy Allocation (Demand Paging):  sbrk() and stack growth will only reserve address space. Physical pages are allocated on the first access, inside the page fault handler. This reduces memory use for processes that allocate more than they touch. Files: kernel/trap.c, kernel/vm.c, kernel/sysproc.c
2. Copy-on-Write (COW) Fork: fork() will share the parent's pages with the child as read-only instead of copying them. A write triggers a page fault, and only then is the page copied. A reference count per physical page will be added to the allocator. Files: kernel/vm.c, kernel/kalloc.c, kernel/trap.c
3. Page Replacement with Swap (Clock/LRU): When physical memory runs out, a victim page will be selected using the Clock (or LRU) algorithm and written to a swap area on disk. It will be brought back on a later page fault. Files: kernel/vm.c, kernel/kalloc.c, kernel/fs.c
Scheduling
4. Multilevel Feedback Queue (MLFQ) Scheduler: The round-robin scheduler will be replaced by multiple priority queues with different time slices and periodic priority boosting (aging) to prevent starvation. A getpinfo() system call will expose per-process queue and tick statistics. Files: kernel/proc.c, kernel/proc.h, kernel/syscall.c
5. Priority Inheritance: A lock holder will temporarily inherit the priority of the highest-priority waiting process, solving the priority inversion problem. A demo program will show the problem before and after the fix. Files: kernel/proc.c, kernel/sleeplock.c
Concurrency
6. Kernel Threads and Synchronization Primitives: clone() and join() system calls will create threads that share an address space. User-level mutex, semaphore, and condition variable primitives will be built on top. Files: kernel/proc.c, kernel/sysproc.c, user/
7. Signals Support for signal(), sigreturn(), and alarm(), allowing user-level handlers for events such as timer expiry and kill. Files: kernel/proc.c, kernel/trap.c, kernel/syscall.c
IPC and File System
8. mmap / munmap and Shared Memory: File-backed and anonymous memory mappings, plus shared memory regions that multiple processes can map. Files: kernel/vm.c, kernel/trap.c, kernel/file.c
9. procfs: (/proc) A virtual file system exposing kernel information, such as /proc/meminfo, /proc/cpuinfo, and /proc/<pid>/status. Files: kernel/fs.c, kernel/file.c, kernel/proc.c
Security
10. Users, Groups, and File Permissions: Per-process uid/gid, per-file ownership and permission bits, chmod and chown system calls, and stricter validation of system call arguments. Files: kernel/proc.h, kernel/fs.h, kernel/sysfile.c

