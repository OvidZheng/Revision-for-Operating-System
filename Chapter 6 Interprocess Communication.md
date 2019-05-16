# Chapter 6 Interprocess Communication
##Introducing IPC(Inter-Process Communication)
### Types Concurrency
* **Pseudo-concurrency** where several processes/threads share a single CPU
* **Physical concurrency** where several processes execute in parallel on several CPUs/cores

### Design Issue of Concurrency
* Communication among processes
* Sharing /Competing of resources
* Synchronization of multiple processes
* Allocation of processor time

### Difficulties with Concurrency
* Sharing global resources
* Management of allocation of resources
* Programming errors difficult to locate

### Process Interaction
#### Processes unaware of each other
* Competition
* Mutual exclusion, Deadlock, Starvation

#### Processes indirectly aware of each other
* Cooperation by sharing
* Mutual exclusion, Deadlock, Starvation, Data coherence

#### Process directly aware of each other
* Cooperation by communication
* Deadlock, Starvation  


### Mutex Exclusion
* Critical sections
* Only one program at a time is allowed in its critical section.
* Eg.  Only one process at a time is allowed to send command to the printer（critical resource）.

### 2 Mechanism For IPC
* Shared Memory
* Passing Message


## Shared Memory
### 2 Ways to share memory
* Threads share a single virtual memory
* Processes share memory by mapping the same physical page frames into their virtual memories

### Mutex Exclusion 1

```
while(TRUE)
{
block_interrupts();
critical_region();
unblock_interrupts();
noncritical_region();
}
```
* Stopping context switches does not prevent two processes/threads on separate CPUs/cores entering the critical region at the same time

### Mutex Exclusion 2

```
// Process 0
while(TRUE)
{
while(turn != 0)
/* DO NOTHING */;
critical_region;
turn = 1;
noncritical_region()
}
```


```
// Process 1
while(TRUE)
{
while(turn != 1)
/* DO NOTHING */;
critical_region;
turn = 0;
noncritical_region()
}
```

* One problem with this approach is that the process is performing busy waiting/spin locks


### Mutex Exclusion 3 - Peterson's Solution

```
array[self] = TRUE; // interested
turn = self
while(turn == self && array[other] == TRUE)
/* DO NOTHING */;
critical_region();
array[self] = FALSE; // not interested
noncritical_region();
```

* Use an array to keep track of who wants to use the critical region

### Mutex Exclusion 4 - TSL (Test and Set Lock)
#### 具体实现
* 读取Lock的值
* 把读到的值存入寄存器RX中
* 然后给LOCK设置一个非0的值(设置到LOCK对应的内存中)
以上三个步骤是一个不可拆分的原子操作,执行该指令的CPU将会锁住内存总线(memory bus),所以在该指令执行完成之前其他CPU是无法访问内存的。

#### TSL和中断屏蔽的区别

当一个CPU将中断屏蔽后,只影响当前屏蔽中断的CPU,其他CPU还是依然可以照样访问内存的(想要中断)。唯一一个当一个CPU在访问内存时阻止其他CPU访问内存的方法就是将内存总线锁住,这个需要硬件的支持,TSL可以达到该目的。（此处需要深入理解：单处理器中使用中断屏蔽，即可在PSW中打开/关闭中断标志位，而由于上述原因，中断屏蔽对于多处理器根本没有效果）

 
#### 深入理解：忙等待和让权等待：
* 只有在有理由认为等待时间非常短的情形下，才只用忙等待。用于忙等待的锁，称为**自旋锁**。
* **忙等待**：连续测量一个变量直到某个值出现是为止。
* **让权等待**：临界区外运行的进程不得阻塞其他进程，出让cpu

#### 深入理解：区别自旋锁和互斥锁
* **自旋锁**：表示一直在原地旋转，并未去睡眠。可能存在的问题：死锁，过多占用cpu资源
* **互斥锁**：如果资源被占用，资源申请者就会进入睡眠状态

> 以上来自网络，我觉得写得很好就复制过来了，感谢作者
> 作者：aut_whisper 
> 来源：CSDN 
> 原文：https://blog.csdn.net/weixin_42682806/article/details/84205156

### Mutex Exclusion 5 - Exchange(XCHG)

### Advantage
* Applicable to any number of processes on **either a** **single processor or multiple processors** sharing main memory.
* It is **simple** and therefore easy to verify.
* It can be used to support **multiple critical sections**.

### Disadvantage
* **Busy-waiting** is employed.
* **Starvation** is possible. 
* when a process leaves a critical section and more **than one process is waiting**.  
* **Deadlock** is possible. 
If a low priority process has the critical region and a higher priority process needs, the higher priority process will obtain the processor to wait for the critical region.


### Busy Waiting
* The process waiting for the critical region wastes (a lot of) CPU time
* The solution is to block the process while it is waiting

### Sleep and Wake Up
* **sleep()** – tells the operating system that the process is waiting for a critical region and to move it to the **blocked queue**
* **wakeup()** – tells the operating system to wakeup another process

### Producer & Customer Problem

```
源网址：https://blog.csdn.net/xyisv/article/details/80467668
#include <stdio.h>
#include <pthread.h>
#include <windows.h>
#define N 100
#define true 1
#define producerNum  10
#define consumerNum  5
#define sleepTime 1000

typedef int semaphore;
typedef int item;
item buffer[N] = {0};
int in = 0;
int out = 0;
int proCount = 0;
semaphore mutex = 1, empty = N, full = 0, proCmutex = 1;

void * producer(void * a){
	while(true){
		while(proCmutex <= 0);
		proCmutex--;
		proCount++;
		printf("生产一个产品ID%d, 缓冲区位置为%d\n",proCount,in);
		proCmutex++;

		while(empty <= 0){
			printf("缓冲区已满！\n");
		}
		empty--;

		while(mutex <= 0);
		mutex--;

		buffer[in] = proCount;
		in = (in + 1) % N;

		mutex++;
		full++;
		Sleep(sleepTime);
	}
}

void * consumer(void *b){
	while(true){
		while(full <= 0){
			printf("缓冲区为空！\n");
		}
		full--;

		while(mutex <= 0);
		mutex--;

		int nextc = buffer[out];
		buffer[out] = 0;//消费完将缓冲区设置为0

		out = (out + 1) % N;

		mutex++;
		empty++;

		printf("\t\t\t\t消费一个产品ID%d,缓冲区位置为%d\n", nextc,out);
		Sleep(sleepTime);
	}
}

int main()
{
	pthread_t threadPool[producerNum+consumerNum];
	int i;
	for(i = 0; i < producerNum; i++){
		pthread_t temp;
		if(pthread_create(&temp, NULL, producer, NULL) == -1){
			printf("ERROR, fail to create producer%d\n", i);
			exit(1);
		}
		threadPool[i] = temp;
	}//创建生产者进程放入线程池


	for(i = 0; i < consumerNum; i++){
		pthread_t temp;
		if(pthread_create(&temp, NULL, consumer, NULL) == -1){
			printf("ERROR, fail to create consumer%d\n", i);
			exit(1);
		}
		threadPool[i+producerNum] = temp;
	}//创建消费者进程放入线程池


	void * result;
	for(i = 0; i < producerNum+consumerNum; i++){
		if(pthread_join(threadPool[i], &result) == -1){
			printf("fail to recollect\n");
			exit(1);
		}
	}//运行线程池
	return 0;
}
```


## Semaphores
Special variable called a semaphore is used for signaling.

### Binary Semaphore
* provide mutual exclusion


```
while(TRUE)
{
wait();
critical_region();
signal();
noncritical_region();
}

```

#### wait()
* If the variable is 1, set it to zero
* If the variable is 0, block

#### signal()
* Set the variable to 1 and wake up any blocked processes



## Classical IPC Problem
### Dining Philosophers Problem
* Suppose that all five philosophers take their left forks simultaneously.
* None will be able to take their right forks, and there will be a deadlock.


```
Program diningphilosophers;
Var  fork:array [0..4] of semaphore (:= 1);
		 i : integer;
procedure philosopher (i : integer);
begin
	repeat
		think;			/*  philosopher is thinking*/
	 wait(fork[i]);			/* take left fork*/
	 wait(fork[(i + 1) mod 5]);	 /* take right fork;*/
	 eat;				/*  eating*/
	 signal(fork[(i + 1) mod 5]);/*  put right fork */
	 signal(fork[i]);    /*  put left fork back on the table*/
forever
end;			
begin
	parbegin
		philosopher(0); philosopher(1); philosopher(2); philosopher(3); philosopher(4);
	parend

```

#### Solution 1
* Allow only **four(n-1)** philosophers to sit at the table
* At least one philosopher will be able to pick up second fork, eat, and return to thinking


```
Program diningphilosophers;
Var  fork:array [0..4] of semaphore (:= 1);
			room : semaphore (:= 4);
i : integer;
procedure philosopher (i : integer);
begin
	repeat
		 think;		/*  philosopher is thinking*/
		 wait(room);    /*  5th philosopher will be blocked in the room semaphore queue */
		 wait(fork[i]);			/* take left fork*/
		 wait(fork[(i + 1) mod 5]);	 /* take right fork*/
		 eat;				/*  eating*/
		 signal(fork[(i + 1) mod 5]); /*  put right fork back on the table*/
		 signal(fork[i]);    /*  put left fork back on the table*/
		 signal(room); /*  wake up the philosopher blocked in the room  semaphore queue  */
	 forever
end;			
begin
	parbegin
		philosopher(0); philosopher(1); philosopher(2); philosopher(3); philosopher(4);
	parend
end.

```

#### Solution 2
* Allow a philosopher to pick up **only two forks at a time**
* Requires a semaphore or similar mechanism to protect critical region



#### Solution 3 
* Use an asymmetric solution, e.g.,
* Philosophers are numbered
* **Odds** pick up **left** fork first
* **Evens** pick up **right** fork first 


### Writer & Reader Problem
* Any number of readers may simultaneously read the file.
* Only one writer at a time may write to the file.
* If a writer is writing to the file, no reader may read it.


```
var rmutex, wmutex:semaphore:=1,1 /* mutex semaphore, 
initialize 1 */
Readcount: integer:=0;   /*  number of readers*/
Begin
Parbegin                           			
   Reader:begin
   repeat                           		     	
	wait(rmutex);                        		
	if readcount = 0 then wait (wmutex);
	   Readcount : = Readcount +1;
	signal(rmutex);     
	   read     
   wait(rmutex);
   Readcount : = Readcount - 1;
	if readcount = 0 then signal(wmutex);
	signal(rmutex);  
	until false;                 
end 			

```


```
Writer:begin
		Repeat
			  Wait(wmutex);
			  write
			  Signal（wmutex);
		  Until false;
		end
	Parend
end

```



## Monitors
* Monitors are a high level **mechanism** provided by programming languages to solve this problem
    * Allow a number of variables and/or functions to be grouped together for a critical region
    * Only **one process** can be active in a monitor at **any one time**

* Monitor is a **software module**
    * **Local data** variables are accessible **only by the monitor**.
    * Process enters monitor by invoking one of its procedures.
    * Only **one process** may be executing in the monitor at a time.




## Message passing
* Message passing involves the introduction of two system calls for exchanging small data (messages)
    * **receive()** – receive a message and/or block until one is received
    * **send()** – send a message to another process
May also block if there aren’t enough OS buffers to store the message

### Addressing
#### Direct Addressing
* send primitive includes a **specific identifier** of the destination process.
* receive primitive could know ahead of time which process a message is expected.
* receive primitive could use source parameter to return a value when the receive operation has been performed.

#### Indirect Addressing
* messages are sent to a shared data structure consisting of queues.
* queues are called **mailboxes**.
* one process sends a message to the mailbox and the other process picks up the message from the mailbox.






