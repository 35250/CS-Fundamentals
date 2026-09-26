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

# Threads

## 1. Thread

A **thread** is an independent execution path within a process.

A process can have multiple threads. Threads in the same process generally share the process's code, heap, global data, and resources, but each thread has its own **Program Counter, CPU registers, and stack**.

---

## 2. Process vs Thread

| Process                                             | Thread                               |
| --------------------------------------------------- | ------------------------------------ |
| Independent execution environment managed by the OS | Execution path within a process      |
| Has its own memory space and resources              | Shares many resources of its process |
| More isolated from other processes                  | Less isolated from other threads     |
| Contains one or more threads                        | Belongs to a process                 |

**Simple idea:**
**Process = environment/resources**
**Thread = execution path**

---

## 3. User-Level Thread vs Kernel-Level Thread

### User-Level Thread

* Managed by a user-level thread library/runtime.
* The OS does not directly manage each individual thread.
* Thread creation and switching can be handled in user space.
* Threads can still use the OS through system calls when they need OS services.

### Kernel-Level Thread

* Managed directly by the OS kernel.
* The kernel knows and manages individual threads.
* The kernel can schedule threads independently.

**Key difference:**
**User-level thread → managed by user-level library/runtime**
**Kernel-level thread → managed by OS kernel**

# CPU Scheduling Basics

## 1. Concurrency vs Parallelism

### Concurrency

Multiple tasks make progress during the same period, but they do not necessarily execute at the exact same time.

* Possible with a single CPU core.
* The CPU switches between runnable tasks.

### Parallelism

Multiple tasks execute **simultaneously**, typically using multiple CPU cores.

**In short:**

* Concurrency → multiple tasks making progress
* Parallelism → multiple tasks executing simultaneously

---

## 2. CPU Scheduling

CPU scheduling is the mechanism by which the OS decides **which runnable process/thread gets the CPU next**.

It is needed because multiple runnable processes/threads compete for limited CPU time.

---

## 3. Preemptive vs Non-Preemptive Scheduling

### Preemptive Scheduling

The OS can **forcibly take the CPU** from a running process/thread and assign it to another runnable one.

### Non-Preemptive Scheduling

The OS **does not forcibly take the CPU** from a running process/thread. It keeps the CPU until the process finishes or blocks.

**Key difference:**

* Preemptive → OS can interrupt a running process/thread.
* Non-preemptive → running process/thread keeps the CPU until it finishes or blocks.

# CPU Scheduling Algorithms

CPU scheduling algorithms determine **which Ready process/thread gets the CPU next**.

Before applying an algorithm, remember:

* **Arrival Time (AT)** → When a process becomes Ready.
* **Burst Time (BT)** → Total CPU time required by the process.
* **Completion Time (CT)** → Time when the process finishes.
* **Turnaround Time (TAT)** → Total time from arrival to completion.

  * `TAT = CT - AT`
* **Waiting Time (WT)** → Total time spent waiting for CPU.

  * `WT = TAT - BT`
* **Response Time (RT)** → Time from arrival until the process gets CPU for the first time.

  * `RT = First CPU Start Time - AT`

---

# 1. FCFS — First Come, First Served

## Core Idea

The process that **arrives first** gets the CPU first.

It works like a normal queue:

```text
P1 → P2 → P3
```

If P1 arrives first, P1 gets the CPU before P2 and P3.

## How It Works

* Processes enter the Ready Queue according to arrival order.
* The process at the front gets the CPU.
* Basic FCFS is **non-preemptive**.
* Once a process gets the CPU, it keeps it until:

  * it finishes, or
  * it blocks/waits for something such as I/O.

A blocked process is no longer using the CPU, so another Ready process can run.

## Example

```text
P1: AT = 0, BT = 5
P2: AT = 1, BT = 3
P3: AT = 2, BT = 2
```

Execution:

```text
0        5        8       10
|--- P1 ---|-- P2 --|--P3--|
```

## Advantages

* Very simple.
* Easy to implement.
* Fair in terms of arrival order.

## Disadvantage — Convoy Effect

A long process can make many short processes wait behind it.

```text
P1 = 20
P2 = 2
P3 = 2

0                    20   22   24
|------ P1 -----------| P2 | P3 |
```

P2 and P3 are short but must wait for P1.

This is called the **Convoy Effect**.

## Key Point

> **FCFS → earliest arriving Ready process gets the CPU.**

---

# 2. SJF — Shortest Job First

## Core Idea

Among the processes that are **currently Ready**, choose the process with the **smallest Burst Time**.

```text
P1 → BT = 8
P2 → BT = 3
P3 → BT = 5

Order: P2 → P3 → P1
```

## Important: Only Ready Processes Matter

SJF cannot choose a process that has not arrived yet.

Example:

```text
P1: AT = 0, BT = 8
P2: AT = 3, BT = 2
```

At time `0`, only P1 is Ready.

Therefore:

```text
0 ───────── 8
    P1
```

P2 cannot be selected at time `0` because it has not arrived.

## Basic SJF Behavior

* Choose the Ready process with the smallest BT.
* Basic SJF is **non-preemptive**.
* Once a process starts running, it is not forcibly interrupted.
* A newly arrived process with a shorter BT must wait until the current process finishes or blocks.

## Example

```text
P1: AT = 0, BT = 7
P2: AT = 2, BT = 4
P3: AT = 3, BT = 2
```

At time `0`:

```text
Only P1 is Ready → P1 runs
```

At time `7`:

```text
P2: BT = 4
P3: BT = 2

Choose P3
```

Execution:

```text
0          7     9          13
|---- P1 ----| P3 |---- P2 ----|
```

## Key Point

> **SJF → among currently Ready processes, choose the one with the smallest total Burst Time.**

---

# 3. SRTF — Shortest Remaining Time First

SRTF is the **preemptive version of SJF**.

## Core Idea

Choose the Ready process with the **smallest remaining CPU time**.

The important difference is:

```text
SJF  → smallest Burst Time
SRTF → smallest Remaining Time
```

## Why "Remaining"?

A process may have already executed part of its Burst Time.

Example:

```text
P1: BT = 8
```

If P1 has already executed for 3 units:

```text
Remaining Time = 8 - 3 = 5
```

## Preemption

SRTF is **preemptive**.

If a new process arrives with a smaller remaining time than the currently running process, the current process can be interrupted.

Example:

```text
P1: AT = 0, BT = 8
P2: AT = 3, BT = 2
```

At time `3`:

```text
P1 has executed for 3 units.

P1 remaining = 8 - 3 = 5
P2 remaining = 2
```

Since:

```text
2 < 5
```

P1 is preempted.

```text
0    3    5        10
| P1 | P2 |--- P1 ---|
```

After P2 finishes, P1 resumes.

## Key Point

> **SRTF → always prefer the Ready process with the smallest remaining CPU time.**

---

# 4. Priority Scheduling

## Core Idea

Among the Ready processes, the process with the **highest priority** gets the CPU.

Example:

```text
P1 → Priority 3
P2 → Priority 1
P3 → Priority 2
```

If **smaller number means higher priority**:

```text
P2 → P3 → P1
```

## Priority Assignment

There is **no single universal formula** for calculating priority.

Priority can depend on:

* Process type/importance.
* Operating-system policy.
* User/application settings.
* Scheduling requirements.
* Dynamic scheduling decisions.
* How long a process has been waiting.

Some systems use **dynamic priorities**, where priority can change over time.

## Important

Always check whether:

```text
Smaller number = higher priority
```

or:

```text
Larger number = higher priority
```

Both conventions can exist.

## Types of Priority Scheduling

Priority Scheduling can be:

### Non-Preemptive Priority

The highest-priority Ready process gets the CPU.

Once it starts:

* It keeps the CPU until it finishes or blocks.
* A newly arriving higher-priority process must wait.

```text
P1 running
   ↓
P2 arrives with higher priority
   ↓
P1 continues
   ↓
P1 finishes
   ↓
P2 runs
```

### Preemptive Priority

A newly arriving higher-priority process can interrupt the currently running process.

```text
P1 running
   ↓
P2 arrives with higher priority
   ↓
P1 is preempted
   ↓
P2 runs
   ↓
P2 finishes
   ↓
P1 resumes
```

## Key Point

> **Priority Scheduling → highest-priority Ready process gets preference.**

Priority Scheduling can be **preemptive or non-preemptive**.

---

# 5. Round Robin (RR)

## Core Idea

Round Robin gives each Ready process a fixed maximum amount of CPU time called the:

**Time Quantum / Time Slice**

Example:

```text
Time Quantum = 2

P1 → P2 → P3

CPU:
P1 → 2 units
P2 → 2 units
P3 → 2 units
P1 → 2 units
...
```

Round Robin is **preemptive**.

## How the Ready Queue Works

Round Robin uses a **circular FIFO-style Ready Queue**.

Example:

```text
P1 → P2 → P3
```

P1 gets one quantum.

If P1 does not finish:

```text
P2 → P3 → P1
```

P2 gets its turn.

Then:

```text
P3 → P1 → P2
```

And so on.

## What Happens When Quantum Expires?

If the process is still running when its quantum expires:

1. OS preempts the process.
2. Process is not finished.
3. It is moved to the **back of the Ready Queue**.
4. The next Ready process gets the CPU.

Example:

```text
Time Quantum = 2

P1 needs 5 units.

P1:
0 → 2
```

P1 has:

```text
5 - 2 = 3 units remaining
```

So:

```text
P2 → P3 → P1
```

P1 waits for its next turn.

## What If the Process Finishes Before the Quantum?

The Time Quantum is a **maximum**, not a guaranteed amount.

Example:

```text
Time Quantum = 4
P1 has only 2 units remaining
```

P1 runs for 2 units and finishes.

The unused 2 units are **not transferred** to the next process.

The next process gets its own fresh quantum:

```text
P1 → 2 units → finishes
P2 → up to 4 units
```

There is no:

```text
P2 → 2 + 4 = 6 units
```

## What If New Processes Arrive During a Quantum?

Suppose:

```text
Time Quantum = 4

P1 arrives at 0
P2 arrives at 1
P3 arrives at 3
```

P1 runs:

```text
0 ───────── 4
    P1
```

P2 and P3 enter the Ready Queue while P1 is running:

```text
P2 → P3
```

At time `4`, P1's quantum expires.

If P1 hasn't finished, it goes to the back:

```text
P2 → P3 → P1
```

Therefore P2 runs next.

## Does Round Robin Use Burst Time or Priority?

No.

Round Robin does **not** choose the next process based on:

* shortest Burst Time
* longest Burst Time
* priority
* remaining Burst Time

Instead, it follows the **Ready Queue order**.

Processes normally enter the queue according to their arrival/order of becoming Ready.

## Key Point

> **Round Robin → give each Ready process up to one Time Quantum, then move unfinished processes to the back of the queue.**

---

# Quick Comparison

| Algorithm       | Selection Rule          | Preemptive? | Main Idea                |
| --------------- | ----------------------- | ----------- | ------------------------ |
| **FCFS**        | Earliest arrival        | No          | First come, first served |
| **SJF**         | Smallest Burst Time     | No          | Shortest job first       |
| **SRTF**        | Smallest Remaining Time | Yes         | Preemptive SJF           |
| **Priority**    | Highest Priority        | Yes/No      | Priority decides         |
| **Round Robin** | Ready Queue order       | Yes         | Fixed Time Quantum       |

## One-Line Mental Models

```text
FCFS     → Who came first?
SJF      → Who needs the least CPU time?
SRTF     → Who needs the least CPU time from now?
Priority → Who has higher priority?
RR       → Whose turn is it in the queue?
```

## Important Interview Distinctions

### SJF vs SRTF

```text
SJF  → Total Burst Time
SRTF → Remaining Burst Time
```

SJF is non-preemptive; SRTF is preemptive.

### FCFS vs Round Robin

```text
FCFS → Once started, process keeps CPU until finish/block.
RR   → Process gets only one quantum, then may be preempted.
```

### Priority vs SJF

```text
SJF      → Burst Time determines preference.
Priority → Priority value determines preference.
```

### Round Robin

Round Robin does not care whether a process has:

```text
BT = 2
BT = 20
BT = 100
```

It gives each Ready process a turn based on the queue and Time Quantum.

---

# Core Summary

```text
FCFS
  ↓
Arrival order

SJF
  ↓
Smallest Burst Time

SRTF
  ↓
Smallest Remaining Time

Priority
  ↓
Highest Priority

Round Robin
  ↓
Ready Queue + Time Quantum
```

These five algorithms cover the main CPU scheduling concepts needed for interview-level OS preparation.

