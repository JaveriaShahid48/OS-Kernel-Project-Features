 Extended xv6-riscv Kernel: Operating Systems Project

Student Name: Javeria Shahid
Roll No: BCS-F24-M48
Course: Operating Systems
Instructor:** Sir Raza

Overview

This project extends the **xv6-riscv** teaching kernel (a re-implementation of Unix Version 6 for RISC-V, written in C) provided as the base by the instructor. The goal is to add six advanced features covering memory management, scheduling, and concurrency.

Base kernel: [mit-pdos/xv6-riscv](https://github.com/mit-pdos/xv6-riscv) (instructor-provided version)
Target platform: RISC-V (QEMU riscv64)

 1. Lazy Allocation
•	Change `sbrk()` so it only increases the process size and does not allocate memory.
•	Handle page faults in the trap handler: on a fault inside the valid address range,
allocate a zeroed physical page and map it.
•	Reject faults outside the valid range (including the stack guard page) by killing the process.
•	Update the unmap and copy routines so that pages that were never allocated are skipped instead of causing a panic.
   Testing: a program that requests a very large heap but touches only a few pages.
 2. Copy-on-Write Fork
•	Add a reference count for every physical page in the allocator, protected by a lock.
•	During `fork()`, map the parent's pages into the child as read-only and mark them as COW. No copying at this stage.
•	On a write fault to a COW page, allocate a new page, copy the data, and map it as writable. If the page has only one owner, simply make it writable.
•	Free a physical page only when its reference count reaches zero.
•	Make sure kernel-side copying to user memory also handles COW pages.
Testing: fork a large process, then compare fork time and memory usage before and after.
3 . MLFQ Scheduler
•	Add a queue level and tick counters to each process.
•	Replace the round-robin `scheduler()` with one that always picks a runnable process from the highest non-empty queue.
•	Give each queue a different time slice. A process that uses its full slice moves down one level.
•	Periodically boost all processes back to the top queue to prevent starvation.
•	Add a `getpinfo()` system call that reports each process's queue level and ticks.
Testing: run one CPU-heavy and one short process and show how their levels change.

 4. Priority Inheritance
•	Store each process's original priority separately from its current priority.
•	Create a lock that records its holder. When a higher-priority process waits on it, temporarily raise the holder's priority.
•	Restore the original priority when the lock is released.
•	Handle interaction with MLFQ demotion and boosting.
Testing:  a demo with low-, medium-, and high-priority processes, showing the delay of the high-priority one with and without inheritance.

5. Kernel Threads and Synchronization
•	Add `clone()` and `join()` system calls. A thread is a new process entry that shares the parent's physical memory but has its own stack and registers.
•	Make sure freeing a thread does not free the memory that is still shared.
•	Build a small user-level thread library on top of `clone()`.
•	Implement spinlock, mutex, semaphore, and condition variable, using two small kernel calls for sleeping and waking on an address.
•	Known limitation: memory growth by one thread is not visible to other threads, since each thread has its own page table.
Testing: several threads updating a shared counter (with and without a lock), and a producer-consumer example.
6. Signals
•	Add per-process fields for registered handlers, pending signals, and alarm settings.
•	Implement `alarm()` first: count timer ticks, and when the interval expires, save the process's registers and redirect execution to the handler.
•	Implement `sigreturn()` to restore the saved registers and resume the interrupted code.
•	Extend this to `signal()` and signal-aware `kill()`, checking pending signals before returning to user mode. SIGKILL cannot be caught.
Testing: an alarm program that counts handler calls, and two processes exchanging a signal.
.
