Chapter 2 Process & Thread

# 1 Introducing Process

* The average operating system will be running many processes, even when it has only just booted

## Process & Program
* Processes are programs in execution
* The difference is subtle, but absolutely crucial. 
    * a computer scientist wants to bake a birthday cake for his daughter
    * He has a birthday cake recipe and all the input: flour, eggs, sugar, extract of vanilla, and so on. 
    * The **recipe is the program**, the computer scientist is the processor (CPU),and the cake ingredients are the input data.
    * The **process is the activity** consisting of our baker reading the recipe, fetching the ingredients, and baking the cake.

## So what is processes?
* processes are a **logical resource**
* The operating system ties together several physical resources and presents them as a new resource referred to a processes
* The concept of a process is supported by **hardware** however

### Task
* A process is the activity of a program on a CPU, also called **Task**
* **Physical Concurrency** = parallelism
    * need **Multiple CPU**
* **Logical Concurrency** = Time-shared CPU



### Characteristics of Process
* Dynamic
* Concurrency
* Independent
* Asynchronous

### Process Structure
* Programs
* Data
* PCB

## Context Switch
* One CPU/core can run **one process** at a time
* The CPU/core is then shared between all processes to give the appearance they are all **running simultaneously**
* This is done many times per second
* This is known as a **context switch**


 

### What is Context
* A **hardware concept** that defines all of the memory that can be accessed/used by program code
* Switching from one process to another means changing **what memory can be accessed**
* One process can’t access the memory of another process
* Hence the term context switch when giving the CPU to another process

### Preemptive Multitasking
* Preempts（抢占） (breaks) the currently executing process and saves its state
* Changes the hardware context to match the next process to be run
* Restores the state of the next process and resume its execution

### How to get to Kernel Mode?
* The operating system executes in kernel mode
* When a process is given the CPU, an instruction is issued to the CPU to switch it to user mode
* There is **no instruction** to switch the CPU back to kernel mode!
    * Only **interrupts** cause the CPU to return to kernel mode
     * Upon receiving an interrupt, the CPU suspends the current execution, **switches to kernel mode**, and invokes a pre-configured（预配置的）kernel routine（程序） to handle the interrupt

#### Category of Interrupts
* **Hardware interrupts**
    * Example: a disk completing a requested read/write operation
* **Software interrupts**
    * Example: “traps” used to implement system calls

#### Timer
* The timer device sends an **interrupt** to the CPU periodically
* The operating system determines how regular these interrupts occur when initializing the hardware at boot time

### Which Process to switch to?
#### The State of Process
* Running
* Ready
* Blocked
* Ready Suspend
* Block Suspend
* New
* Exit

#### Process Control Block
 * The operating system kernel tracks which resources belong to which process by storing the information in a data structure known as the **Process Control Block (PCB)**
    * Process identification number (PID)
    * Program counter and register values (when process is not running)
    * Memory information (information to rebuild the hardware context)
    * Related processes (parent PID, child PIDs, etc.)
    * Accounting information (owner, run time, etc.)
    * Links to other resource data structures (open files, network connections, etc.)

### When to Switch a Process?
* Clock Interrupt
* I/O Interrupt
* Memory Fault

### Process Switch & Mode Switch
#### Process Switch
* An operation **between the processes**. 
* When the dispatcher to recover the current process and dispatch CPU to another ready process, the operation will be referenced.

#### Mode Switch
* An operation **in one process**. 
* When the process image calls a system calls provided by kernel subsystems, the operation will be referenced.


# 2 Process Creation & Process Termination
## 4 types Process Creation
* Submission of a batch job
* User logs on
* Created to provide a service such as printing
* Process creates another process

## The content of a Executable File
* Descriptopn of Memory Requirement
* Text Region
* Data Region

## The Procedure of Creation
* Allocate and initialize a **PCB structure**, including initial **register values**
* Allocate at least three memory regions:
    * Text: Contains the code loaded from the executable file
    * Data/heap
    * Stack region: Used for intermediate results, local variables, function call parameters etc. (also for system calls), etc.
    * Connect standard input/output streams (console input and output) to the process
* Initialize **hardware context** to match new process
* Switch to **user mode**
* Jump” (set program counter) to pre-defined code entry point

## 4 Types of Termination
* Batch job issues Halt instruction
* User logs off
* Quit an application
* Error and fault conditions

## Procedure of Termination
* Program invokes a system call to the operating system for self-termination, indicating a result
* Operating system stores the result in the PCB and frees all memory used by the process
* PCB remains in a ‘terminated’ state until the parent process retrieves the result

# 3 Introduce Threads
## Threads State
* Running
* Ready
* Blocked

## 3 Ways to implements Threads
### User-Level Threads
* All thread management is done by the **application**.
* The kernel is **not aware** of the existence of threads.

### Kernel-Level Threads
* Kernel maintains context information for the process and the threads.
* Scheduling is done **on a thread basis**.

### Combined Approched
* Thread creation done in the user space.
* Bulk of scheduling and synchronization of threads done in the user space.

## Benefits of Threads
* Takes less time to **create** a new thread than a process.
* Less time to **terminate** a thread than a process.
* Less time to **switch** between two threads within the same process.
* Since threads within the same process share memory and files, they can **communicate** with each other **without invoking the kernel**.
* A process can accomplish more than one thing at a time

## Disadvantage of threads
* An error in one thread can cause the entire program to terminate/crash
* Threads can access/modify the same area of memory at the same time, causing corruptions
