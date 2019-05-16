# Chapter 3 Process Scheduling
## Introduction to Scheduling

### Non-Preemptive Scheduling
* Once a process is in the running state, it will continue until it terminates or blocks itself for I/O

### Preemptive Scheduling
* Currently running process may be interrupted and  moved to the Ready state by the operating system

## Objective of Scheduling algorithms
### Fairness（公平）
* Similar jobs should get **similar service** (time on the CPU)

### Policy enforcement
* Some jobs may have **unusual requirements**
* e.g., a safety control system should get the CPU as soon as it’s ready to run


### Balance
* Keep as much of the system **busy** as possible
* e.g., allow I/O bound jobs to run to keep peripherals working in parallel with the CPU

### Throughput（吞吐量）
* The **number** of jobs completed **per unit of time**


### Turnaround time（周转时间）
* The time from when a job is first **submitted** until it is **completes/exits**

### CPU utilization
* Keep the CPU **working at all times**, an idle CPU is wasted time
*Remember a context switch is wasted time!*

### Response time
* The **time**it takes for a program to **respond to input**
* e.g., typing in a command to the shell in Linux


### Proportionality（均衡性）
* System matches **user expectations**
* e.g., simple jobs are quick, complex jobs take time


### Meeting deadlines
* Real-time systems work to deadlines
* e.g., shutting down a runaway nuclear reaction
 
### Predictability（可预测性）
* Missing the occasional deadline doesn’t matter, but if the processes don’t get the CPU with **foreseeable/expectable regularity** they cannot function properly
* e.g., video/audio streaming

 

## Types of Scheduling
### By OS Type
#### Batch system
* Non-interactive
* e.g., calculate interest (banks)

####Interactive system
* Multi-user interactive systems,
* e.g., Linux

#### Real-time system
* Meeting deadlines is most important
* e.g., monitoring a nuclear reactor


#### Batch system objectives
* Throughput
* turnarond time
* CPU utilisation

#### Interactive system objectives
* Response time
* proportionality（均衡性）

#### Real-time system objectives
* Meeting deadlines
* predictability

#### All system objectives
* Fairness
* policy enforcement
* balance

### By Scheduling Hierarchical
#### Long-term Scheduling
* Determines which programs are **admitted to the system for processing**
   
* **How many** programs are admitted to the system ? 
    * Controls the degree of multiprogramming
* **When** does the scheduler be invoked?
    * Each time a job terminates
    * Processor is idle exceeds a certain threshold


#### Medium-term Scheduling
* It schedules the process **swapped out to disk** in memory, ready to execute


#### Short-term scheduling
* Schedule the **READY process to run** 
* Executes most frequently
* Invoked when an event occurs
	* Clock interrupts
	* I/O interrupts
	* Operating system calls
	* Signals(信号) 

## Scheduling Algorithms
#### First Come First Serve(FCFS)
* Advantages
    * Simple
    * Low overhead（开销） (scheduling does not take away time from processes)
* Disadvantages
    * Favours CPU bound jobs over I/O bound jobs
    * Not useful for interactive systems
    * Disadvantages short jobs

### Shortest Job First(SJF)
* Advantages
    * The smaller the execution time the better the turnaround time of the job
* Disadvantages
    * User must estimate the execution time – how?
    * If enough short jobs are arriving, starvation is possible for longer jobs (may never run)

### Shortest Remaining Time Next(SRTN)
* Advantages
    * The smaller the execution time the better the turnaround time of the job
    * Can prove it’s optimal for the shortest average turnaround time
* Disadvantages
    * User must estimate the execution time – how?
    * If enough short jobs are arriving, starvation is possible for longer jobs (may never run)

### Round Robin(RR)
* Advantages
    * Usable for interactive systems
    * Simple
* Disadvantages
    * If the timeslice is too long, performance approaches FIFO behaviour
        * If the timeslice is too short, the system thrashes because the context switch overhead dominates
        * Very difficult to select a perfect timeslice

### Types of Priority
#### Static Priority
* Never change throughout the lifetime of a process
* Easy to implement
* Low overhead

#### Dynamic Priority
* Priorities adapt to the observed execution of the process
* Calculating priorities adds overhead
* The hope is the overhead is less significant than the increased effectiveness of scheduling

### Multiple Queues
* Highest priority contains **I/O bound processes**
    * Scheduled regularly for short timeslices
* Lowest priority contains **CPU bound processes**
    * Scheduled less-regularly for long timeslices
* Top priority queue gets one time quantum, i.e., timeslice = x
* Next priority gets two time quantum, i.e., timeslice = 2x
* Next priority gets four time quantum, i.e., timeslice = 4x
… continues doubling time quantum each time

### Types of Task
#### Periodic Task
* They occur at regular intervals

#### Aperiodic Task
* They occur unpredictably

#### Hard-Realtime Task
* There are absolute deadline that must be met

#### Soft-Realtime Task
*  Missing deadline is toleratble


#### Characteristics of Realtime System
* Deterministc
* Responsiveness
* User Control
* Reliability