# 🔥 Your Actual OS Syllabus

## Tier 1 — MASTER

-> Processes & Threads

-> Process
-> Process states
-> PCB
-> Context switching
-> Process vs thread
-> User vs kernel threads
-> System calls
-> User vs kernel mode
-> Concurrency vs parallelism

-> CPU Scheduling
-> Preemptive vs non-preemptive
-> FCFS
-> SJF
-> SRTF
-> Priority
-> Round Robin
-> Scheduling metrics
-> Starvation + aging

-> Synchronization
-> Race condition
-> Critical section
-> Mutual exclusion
-> Mutex
-> Semaphore
-> Binary/counting semaphore
-> Producer-consumer

-> Deadlocks
-> Deadlock
-> Four conditions
-> Prevention
-> Avoidance
-> Detection
-> Deadlock vs starvation

-> Memory
-> Logical vs physical address
-> Contiguous allocation
-> Internal/external fragmentation
-> Paging
-> Page table
-> Virtual memory
-> Demand paging
-> Page fault
-> FIFO/LRU/Optimal
-> TLB

## Tier 2 — UNDERSTAND
-> fork/exec/wait/exit
-> User vs kernel threads deeper details
-> IPC
-> Multilevel Queue
-> MLFQ
-> Readers-writers
-> Dining philosophers
-> Resource Allocation Graph
-> Banker's Algorithm concept
-> Segmentation
-> File systems
-> Inodes
-> Basic Linux commands


# Operating System — Fundamentals

## 1. Operating System

An **Operating System (OS)** is system software that manages hardware resources and provides services to applications.

Applications need resources such as:

* CPU
* Memory
* Storage
* Files
* I/O devices
* Network resources

The OS acts as a **resource manager and abstraction/protection layer** between applications and hardware.

```text
Application
     ↓
Operating System
     ↓
Hardware
```

### Why do we need an OS?

Applications should not have unrestricted access to hardware because:

* Multiple applications may need the same resources.
* Uncontrolled access can cause applications to interfere with each other.
* A buggy or malicious application could affect other applications or the entire system.
* Hardware operations need to be managed in a controlled and efficient manner.

Therefore, the OS manages access to hardware and provides applications with controlled services.

---

## 2. User Mode and Kernel Mode

Modern systems separate execution into different privilege levels. The two important ones are:

### User Mode

Applications normally execute in **User Mode**.

* Limited privileges
* Cannot directly perform privileged operations
* Cannot freely access protected hardware resources

### Kernel Mode

The OS kernel executes in **Kernel Mode**.

* Higher privileges
* Can perform privileged operations
* Can manage protected resources and hardware on behalf of applications

### Why the separation?

The separation provides **protection and controlled access**.

An application should not be able to directly manipulate critical system resources simply because it is running on the machine.

```text
Application Code
      ↓
  User Mode
  (Limited privileges)

      ↓ System Call

  Kernel Mode
  (High privileges)
      ↓
   OS Kernel
      ↓
Hardware / Protected Resources
```

---

## 3. System Call

A **system call** is a controlled interface through which a user-space application requests a service from the OS kernel.

Applications use system calls when they need an OS-managed or privileged service.

Examples include operations related to:

* Files
* Processes
* Memory
* I/O
* Inter-process communication

A system call is **not a message that physically travels from the application to the OS**.

Instead, invoking a system call causes the CPU to **transfer control from user-space code to the appropriate kernel-space code**, with the CPU operating at the required privilege level.

---

## 4. System Call Execution Flow

Consider an application that wants to read data from a file.

### Step 1 — Application executes

The application is executing its code in **User Mode**.

```text
Application
    ↓
User Mode
```

### Step 2 — Application invokes a system call

The application invokes a system call such as `read()` to request the required OS service.

```text
Application
    ↓
read() system call
```

### Step 3 — CPU transfers control to the kernel

The system-call mechanism causes a **controlled transition**:

```text
User Mode
    ↓
Kernel Mode
```

The CPU stops executing the application's user-space instructions and begins executing the appropriate kernel code.

The application itself does **not become kernel code**.

### Step 4 — Kernel handles the request

The kernel receives the request and performs the required operations.

It may:

* Validate the request
* Check permissions
* Locate the required file/data
* Interact with the filesystem
* Communicate with the appropriate device or storage mechanism

### Step 5 — Kernel prepares the result

The kernel obtains the requested data or determines the appropriate result/error.

### Step 6 — Control returns to the application

After the system call completes, control returns from the kernel to the application.

```text
Kernel Mode
    ↓
Return from system call
    ↓
User Mode
```

The CPU resumes executing the application's user-space code.

### Complete flow

```text
┌──────────────────────────┐
│       Application        │
│                          │
│        User Mode         │
└────────────┬─────────────┘
             │
             │ Invoke System Call
             ▼
┌──────────────────────────┐
│   System Call Mechanism  │
└────────────┬─────────────┘
             │
             │ Controlled transition
             ▼
┌──────────────────────────┐
│          Kernel          │
│                          │
│       Kernel Mode        │
│                          │
│   Handles the request    │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│ Hardware / OS Resources  │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│          Kernel          │
│     Prepares result      │
└────────────┬─────────────┘
             │
             │ Return from system call
             ▼
┌──────────────────────────┐
│       Application        │
│                          │
│        User Mode         │
│   Continues execution    │
└──────────────────────────┘
```

### Key distinction

The important transition is the **CPU's execution context and privilege level**:

```text
Application code
      ↓
   User Mode
      ↓
System-call mechanism
      ↓
   Kernel Mode
      ↓
Kernel code executes
      ↓
Request is handled
      ↓
   User Mode
      ↓
Application code continues
```

The system call provides the **controlled entry point**; the kernel performs the requested OS service.

