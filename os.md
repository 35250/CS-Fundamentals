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


## 1. Program vs Process

### Program

A **program is a passive set of instructions stored on secondary storage that describes what should be executed.**

* Passive → it is not currently executing.
* Contains instructions/code, but not the current execution state.
* Example: an executable file sitting on disk.

### Process

A **process is an active instance of a program that is currently executing and managed by the OS.**

A process requires more than just the program's instructions, such as:

* Current process state
* CPU execution state
* Memory allocated to it
* Resources being used
* Process identification and management information

### Key Difference

> **Program = what should be executed**
> **Process = an actual execution of that program + the information required to manage it**

One program can have **multiple processes** running from it.

---

## 2. Process States

A **process state describes the current condition of a process and whether it can currently execute.**

### Main Process States

| State                 | Meaning                                                                                       |
| --------------------- | --------------------------------------------------------------------------------------------- |
| **New**               | Process is being created                                                                      |
| **Ready**             | Process can execute but is waiting for the CPU                                                |
| **Running**           | Process is currently executing on the CPU                                                     |
| **Waiting / Blocked** | Process cannot continue until an event, I/O operation, or required resource becomes available |
| **Terminated**        | Process has finished execution or has been terminated                                         |

### Important Distinction

> **Ready = waiting for CPU**
> **Waiting/Blocked = waiting for an event/resource**

For example:

```text
Process requests disk data
        ↓
Cannot continue
        ↓
Waiting / Blocked
        ↓
Disk operation completes
        ↓
Ready
        ↓
CPU assigned
        ↓
Running
```

### Why Does the OS Need Process States?

Process states allow the OS to know **whether a process can currently make progress**.

If a process is waiting for I/O, keeping it on the CPU would waste CPU time. The OS can instead run another Ready process.

> **Process states help the OS manage processes and use the CPU efficiently.**

---

## 3. Process Control Block (PCB)

### Definition

A **Process Control Block (PCB) is a data structure maintained by the OS that contains the information required to manage a process.**

Think of it as:

> **PCB = OS's record of a process**

A PCB can contain information such as:

* Process ID
* Process state
* Program Counter
* CPU registers / CPU execution context
* Scheduling information
* Memory-management information
* Resource information

Conceptually:

```text
PCB
├── Process ID
├── Process State
├── Scheduling Information
├── Memory Information
├── Resource Information
└── CPU Execution Context
      ├── Program Counter
      ├── CPU Registers
      └── Other CPU State
```

### Process State vs CPU Execution State

**Process State:** What is the process currently doing?

Examples:

* Ready
* Running
* Waiting

**CPU Execution State:** What was the CPU's execution situation for this process?

Examples:

* Program Counter
* CPU Registers
* Other architecture-dependent CPU state

---

## 4. Context Switching

### Context

The **CPU context is the execution state that must be preserved so that a process can correctly resume execution later.**

It includes information such as:

* Program Counter
* CPU registers
* Other architecture-dependent CPU state

### Context Switching

A **context switch is the process of saving the execution context of the currently running process and restoring the context of another process so the CPU can switch execution between them.**

Example:

```text
CPU
 ↓
Process A
 ↓
Save A's context
 ↓
Load B's context
 ↓
Process B
```

Later:

```text
Process B
 ↓
Save B's context
 ↓
Load A's context
 ↓
Process A resumes
```

The process **does not start from the beginning**. It resumes from the point where it was interrupted, with the required CPU state restored.

### Why Is Context Switching Needed?

It allows the OS to switch the CPU between processes, enabling **multitasking**.

Context switching can occur when:

* A process blocks for I/O
* A time slice expires
* A higher-priority process needs the CPU
* A process terminates

### Context-Switching Overhead

> **Context switching has overhead because the CPU spends time saving and restoring execution state instead of executing useful application instructions.**

---

## 5. Process Creation

### Definition

**Process creation is the process by which the OS establishes a new process and its execution environment.**

Conceptually:

```text
Program
   ↓
OS creates process
   ↓
Initializes process information
   ↓
Allocates required resources
   ↓
Process becomes Ready
```

A process can also create another process.

### Parent and Child Processes

The process that creates another process is called the **parent process**, while the newly created process is called the **child process**.

A child may **inherit certain information or resources from its parent**, depending on the OS and process-creation mechanism.

However:

> **Parent and child are separate processes with their own process identities and execution contexts.**

---

## 6. Process Termination

### Definition

**Process termination occurs when a process finishes execution or is explicitly terminated by the OS or another authorized process.**

After termination, the OS performs the necessary **cleanup and resource reclamation** associated with that process.

Conceptually:

```text
Running
   ↓
Process finishes / is terminated
   ↓
Terminated
   ↓
OS cleans up resources
```

Resources that may need to be cleaned up include:

* Memory
* Open resources/files
* Process-management information
* Other resources allocated to the process

---

## Key Definitions to Remember

1. **Program:** A passive set of instructions describing what should be executed.

2. **Process:** An active instance of a program that is currently executing and managed by the OS.

3. **Process State:** The current condition of a process that indicates whether and why it can execute.

4. **PCB:** An OS-maintained data structure containing the information required to manage a process.

5. **Context Switching:** Saving one process's CPU execution context and restoring another's so the CPU can switch between them.

6. **Process Creation:** The process by which the OS establishes a new process and its execution environment.

7. **Process Termination:** The process of ending a process and allowing the OS to clean up its associated resources.
