# chapter 1 Introduction to Operating System

## 1 What is an Operating System?
### A working computer consists of
* Hardware
* User Applications
* Operating System

### An Operating System consists of
* Kernel
* System Programs 

### The concept of Operating Sytem
* An Operating System is **a Program** that
    * manages the **computer hardware**
    * provides a **basis** for application programs
    * acts as an intermediary between the computer **user** and the **computer hardware**

* OS is a **resource allocator**
    * **Manages** all resources
    * Decides between **conflicting requests** for efficient and fair resource use

* OS is **a control program**
    *  Controls execution of programs to prevent errors and improper use of the computer


-------


* OS is an **extended machine**
    * Principle of **abstraction** hides complexity
    * OS provides **high** level operations using **lower** level operations
* OS is a **virtual machine**
    * Principle of virtualization supports sharing
    * OS provides virtual CPU, memory, devices
* OS is a **resource manager**
    * Balance overall performance with individual needs (response time, deadlines)


### Two ways to view an OS
#### Makes computer systems easier to uese
* Provides **interface** to run apps
* Provides **authentication** and **Protection**
* Provides read/write **interface**
* Implements **TCP/IP stack**

#### Manage the resources of the computer
* Process management
* Memory management
* Storage management
* Storage management
* Protection management

### Computer hardware provides two modes of OS
#### Kernel Mode
* **Full access** to all hardware and devices
* **Only** the operating system kernel runs here

#### User Mode
* **Limited access** to hardware and devices
    * Accessed through system calls which are processed by the OS kernel
* **All processes** run in this mode

## 2 Evolution of OS
### Serial Processing
* **No** operating system
* Machines run from a console with **display lights** and **toggle switches**, **input device**, and **printer**. 
* Two main problems:                                
    * **Scheduling**: waste processing time.
    * **Setup time**: Setup included loading the compiler, source program, saving compiled program, and loading and linking.

### Simple Batch System
* Monitors: Software that controls the running programs
     * Resident monitor is in main memory and available for execution
* Batch jobs together
* Program branches back to monitor when finished

### Uniprogramming
* Processor must wait for I/O instruction to complete before proceeding.
* Be inefficient.

### Multiprogramming
* Basic problem:
    * Some programs are compute-bound, some I/O-bound
    * Even “balanced” programs are balance only over time
    * No one program can make full use of the system
* Solution: **Multiprogramming**
    * Have more than one active (running) program in memory at any one time
* Multiprogramming requires
    * Sharing resources among different programs
    * Hiding from each program the fact of this sharing
    
#### Difficulties with Multiprogramming
* Improper synchronization （同步）
    * ensure a process waiting for an I/O device receives the signal
* Failed mutual exclusion（互斥）
* Nondeterminate program operation
    * program should only depend on input to it
* Deadlocks（死锁）

### Time sharing
* Using multiprogramming to handle multiple interactive （交互）jobs
* Processor’s time is shared among multiple users
* **Multiple users** simultaneously（同时） access the system through terminals



|  | Batch Multiprogramming  | TimeShare |
| --- | --- | --- |
| Goal | Maximize processor use  | Minimize response time  |
| Source of directives to operating system  | Job control language commands provided with the job  | Commands entered at the terminal  |

### Modes of Excution
* User mode
    * Less-privileged mode
    * User programs typically execute in this mode
* System mode, control mode, or kernel mode
    * More-privileged mode
    * Kernel of the operating system


## 3 What is Kernel
* Just another computer program!
* Run automatically when the system is booted
* Normally terminates when you “shut down” the computer

### Typical Function of an OS Kernel
* Process Management：
    * Process creation and termination, scheduling, state transitions, synchronization and communication, management of PCB
* Memory Management：
    * Assign address space for the process，swap, Paging and segmentation management
* I/O Management：
    * Cache management, allocate I/O channels and devices to process
* Interrupt handling，中断处理
* Timing，时钟管理
* Primitive（原语）: Atomic Operation
* Accounting，统计
* Monitoring，监测

### Ways to structure a Kernel
#### Monolithic
* A single, typically large, program
* All kernel functions can be invoked directly
* Highly efficient, everything is just a function call away!
* Very difficult to maintain/extend
* Difficult to isolate and debug errors
* A bug will usually cause a crash, e.g., Windows’ “blue screen of death” (BSOD)

#### Layered
* Similar to network protocol stack concept
* Similar functions collected into layers
* Each layer interacts only with adjacent layers
    * Each layer provides services to the next higher layer
    * Layers can change without impacting other layers if interface remains unchanged
* Difficult to design, e.g.,
    * I/O requires memory management (what data to read/write)
    * Memory management needs I/O (swapping)



