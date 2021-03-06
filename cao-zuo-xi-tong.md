[TOC]



#  概述

1. 并发

指宏观上一段时间内能同时运行多个程序。

**并发和并行区别**

并发是两个队列交替使用一台咖啡机，并行是两个队列同时使用两台咖啡机。

并发和并行都可以是很多个线程，就看这些线程能不能同时被（多个）cpu执行，如果可以就说明是并行，而并发是多个线程被（一个）cpu 轮流切换着执行。

2. 共享

   共享是指系统中的资源可以被多个并发进程共同使用。

   有两种共享方式：互斥共享和同时共享。

3. 虚拟

4. 异步


# 基本功能

## 1. 进程管理

进程与线程、进程调度、进程通信、线程同步、死锁处理等。

## 2. 内存管理

内存分配、地址映射、内存保护与共享、虚拟内存等。

## 3. 存储管理

 1. 文件系统管理

    文件存储空间的管理、目录管理、文件读写管理和保护等。

2. I/O系统管理

   完成用户的 I/O 请求，方便用户使用各种设备，并提高设备的利用率。

   主要包括缓冲管理、设备分配、设备处理、虛拟设备等。



# 进程管理

## 进程与线程

### 1. 进程(process)：

- 是执行中的程序。

- 进程包括程序代码(文本段)、当前状态（由程序计数器的值和处理器寄存器的内容表示）、堆栈段、堆和数据段。

- 当前状态包括：1. 新的；2. 运行；3. 等待；4.就绪；5. 终止。

- 在操作系统中，每个进程用进程控制块(PCB)表示。

- PCB包含

  	1. 进程ID； 2. 进程状态；3. 程序计数器；4. CPU寄存器；5. CPU调度信息...

### 2. 线程(thread):

- 是CPU使用的基本单元。
- 由线程ID、程序计数器、寄存器集合和栈组成。 
- 它与属于同一进程的其他线程共占代码段、数据段、其他系统资源，独占寄存器和栈。

### 3. 进程和线程的区别

1. 拥有资源

   进程是资源分配的基本单位，但是线程不拥有资源，线程可以访问隶属进程的资源。

2. 调度

   线程是独立调度的基本单位，在同一进程中，线程的切换不会引起进程切换，从一个进程内的线程切换到另一个进程中的线程时，会引起进程切换。

3. 系统开销

   创建或撤销进程时，系统都要为之分配或回收资源线程，开销较大，切换时只需保存和设置少量寄存器内容，开销很小

### 4. 进程状态的切换

![process state.png](https://github.com/Realee007/jobbook/blob/master/src/image/process%20state.png?raw=true) 

- 就绪状态（ready）：等待被调度
- 等待(阻塞)状态（waiting）：等待资源

应该注意以下内容：

- 只有**就绪态和运行态可以相互转换**，其它的都是单向转换。就绪状态的进程通过调度算法从而获得 CPU 时间，转为运行状态；而运行状态的进程，在分配给它的 CPU 时间片用完之后就会转为就绪状态，等待下一次调度。
- 阻塞状态是缺少需要的资源从而由运行状态转换而来，但是该资源不包括 CPU 时间，缺少 CPU 时间会从运行态转换为就绪态。

## 线程的创建

**在Linux中**

`fork()`会产生一个和父进程完全相同的子进程。对于父进程，fork函数返回子进程的pid,而子进程,fork函数返回零。

`exec()` 一个进程调用exec()函数后，它本身就"死亡"了，系统把代码段替换为新程序的代码，废弃原有的数据段和堆栈段，并为新程序分配新的数据段与堆栈段，唯一留下的是PID。也就是说还同一个进程，不过已经是另一个程序。

出于效率考虑，某些UNIX实现了优化的`fork()`即使用**写时复用（copy-on-write）**。也就是只有进程空间的各段的内容要发生变化时，才会将父进程的内容复制一份给子进程。

	COW具体是： 在`fork`之后`exec`之前，两个进程用的是相同的物理空间，子进程的代码段、数据段、堆栈都是指向父进程的物理空间，也就是说，两者的虚拟空间不同，但是其对应的物理空间是同一个。当父子进程中有更改相应段的行为发生时，再为子进程相应的段分配物理空间

```c++
int pid = fork();
 if (pid == 0)
 {
     printf("I'm the child");
 }
 else
 {
     printf("I'm the parent, my child is %i", pid);
     // here we can kill the child, but that's not very parently of us
 }
```



```c++
//ppid = parent process id
//pid =  process id
--------+
| pid=7  |
| ppid=4 |
| bash   |
+--------+
    |
    | calls fork
    V
+--------+             +--------+
| pid=7  |    forks    | pid=22 |
| ppid=4 | ----------> | ppid=7 |
| bash   |             | bash   |
+--------+             +--------+
    |                      |
    | waits for pid 22     | calls exec to run ls
    |                      V
    |                  +--------+
    |                  | pid=22 |
    |                  | ppid=7 |
    |                  | ls     |
    V                  +--------+
+--------+                 |
| pid=7  |                 | exits
| ppid=4 | <---------------+
| bash   |
+--------+
    |
    | continues
    V
```



## 进程调度

### 1. 调度队列

进程调度选择一个可用的进程到CPU上执行。

进程进入系统后，会被加到**作业队列**(job queue)。而驻留在内存中就绪(ready)、等待运行(wait)的进程保存在**就绪队列**(ready queue)，还有等待特定I/O设备的进程放在**设备队列**(devic queue)。

进程的选择是由相应的**调度程序**(scheduler):

-  长期调度程序，从大容量（磁盘）的缓冲池中选择进程并装入内存。

- 短期调度程序，从准备执行的进程中选择进程并为之分配CPU。

### 2. 上下文切换≈ 进程切换

上下文切换(context switch) 

具体操作：

1. 保存旧进程的状态到其PCB中。

2. 装入经调度要执行新进程的上下文。

   ![context switch.png](https://github.com/Realee007/jobbook/blob/master/src/image/context%20switch.png?raw=true) 

### 3. 调度算法

1. 先到先服务(FCFS)：排队买票，先到先服务。
2. 最短作业优先调度(SJF)：有时候我们手上有许多任务要完成时，以最快搞定手头事情，会先完成能最快完成的任务。
3. 最短剩余时间优先调度(SRTF)： 临近期末考试月，会选择以最先开考的课程来复习备考。
4. 优先级调度： 对于冰箱里的食物，总是先吃自己最爱吃的食物，对于不喜欢吃的可能放久了会坏掉（线程饿死）。
5. 轮转法调度(RR)：安排实验室的同学轮流打扫实验室的卫生。

## 进程间的通信

进程的通信方式 IPC(InterProcess Communication)：

- 进程同步：控制多个进程按一定顺序执行；
- 进程通信：进程间传输信息。

### 消息传递(message passing)

在协作进程间交换消息实现通信，进程P和Q通信，首先需要有通讯线。逻辑实现路线和send()/receive()操作的方法：

- 直接或间接通信。

  1. 对于直接通信，需要每个进程必须明确地命名通信的接收者或发送者。

     send(P,message)：发送消息给P,  receive(Q,message)：收到进程Q的消息。

  2. 间接通信

     通过邮箱或者端口来发送个接收消息。邮箱可以抽象成一个对象，进程可以向其中存放、删除消息。

     send(A,message)：发送一个消息到邮箱A， receive(A,message)：接收来自邮箱A的消息。

- 同步或异步通讯。

  同步也称为阻塞，异步称为非阻塞

  1. 阻塞send：发送进程阻塞，直到消息被（接收进程或邮箱）接收。
  2. 非阻塞send：发送进程发送消息并再继续操作。
  3. 阻塞receive：接受者阻塞，直到有消息可用。
  4. 非阻塞receive：接受者收到一个有效消息或空消息。

- 自动或零缓冲。

  通信进程所交换的消息都驻留在临时队列。

  队列的三种实现：

  1. 零容量，阻塞send。
  2. 有限容量，如果队列未满，则非阻塞send，当容量满后，必须阻塞send，直到容量可用。
  3. 无线容量，非阻塞send。

#### 消息队列(MQ)的实现

UNIX下：

```
key_t ftok(const char* pathname, int proj_id);
// 创建唯一键，成功返回唯一键标识符，失败返回-1
// pathname:指定的文件名， proj_id:子序列号(8bit可用0-255)

int msgget(key_t key, int msgflg);
// 建立消息队列。返回其消息队列标识符
// msgflg是不同类型的控制操作符。

int msgsnd(int msgid, struct msgbuf* msgp, int msgsz, int msgflg);
//将消息送入消息队列
//msgid由msgget函数得到，msgp为要发送的消息结构，msgsz为发送消息的长度，msgflg为控制函数行为的标志。

int msgrcv(int msgid,struct masgbuf* msgp, int msgsz, long msgtyp,int msgflg);
//从消息队列中读取消息
//第四个参数为指定从队列中所取消息的类型。
```

```c
// C Program for Message Queue (Writer Process)
#include <stdio.h>
#include <sys/ipc.h>
#include <sys/msg.h>
// structure for message queue
struct mesg_buffer {
	long mesg_type;
	char mesg_text[100];
} message;
int main()
{
	key_t key;
	int msgid;

	// ftok to generate unique key
	key = ftok("progfile", 65);

	// msgget creates a message queue
	// and returns identifier
	msgid = msgget(key, 0666 | IPC_CREAT);
	message.mesg_type = 1;					//指定msgtyp;接收函数需要指定该msgtyp

	printf("Write Data : ");
	gets(message.mesg_text);

	// msgsnd to send message
	msgsnd(msgid, &message, sizeof(message), 0);

	// display the message
	printf("Data send is : %s \n", message.mesg_text);

	return 0;
}

```

```c
// C Program for Message Queue (Reader Process)
#include <stdio.h>
#include <sys/ipc.h>
#include <sys/msg.h>

// structure for message queue
struct mesg_buffer {
	long mesg_type;
	char mesg_text[100];
} message;

int main()
{
	key_t key;
	int msgid;

	// ftok to generate unique key
	key = ftok("progfile", 65);

	// msgget creates a message queue
	// and returns identifier
	msgid = msgget(key, 0666 | IPC_CREAT);

	// msgrcv to receive message
	msgrcv(msgid, &message, sizeof(message), 1, 0);

	// display the message
	printf("Data Received is : %s \n", 
					message.mesg_text);

	// to destroy the message queue
	msgctl(msgid, IPC_RMID, NULL);

	return 0;
}

```



### 共享内存（shared memory）

共享内存就是映射一段能被其他进程所访问的内存，这段共享内存由一个进程创建，但多个进程都可以访问，共享内存是**最快的IPC方式**，它往往与其他通信机制，如信号量配合使用，来实现进程间的同步和通信。 

常见：生产者进程--消费者进程，采用共享内存（缓存）。

### 管道（Pipes）

>A *pipe* is a section of shared memory that processes use for communication.  ---[MSDN--pipes](https://docs.microsoft.com/zh-cn/windows/desktop/ipc/pipes)

管道是共享内存的一种。创建管道的进程称为pipe server, 与管道连接的进程称为pipe client. 进程A向pipe中写入信息，其他进程从pipe中读取信息。

1. 匿名管道（anonymous-pipes）:

   匿名，单向管道(单工)，通常建立在父进程和子进程之间的通信，匿名管道总是本地的; 不能用于通过网络进行通信。 

2. 命名管道（named pipes）:

   遵循FIFO ，任何进程都可以充当服务器和客户端，从而可以对等通信（半双工），可用于在同一计算机上的进程之间或跨网络的不同计算机上的进程之间提供通信。


### 套接字（socket）

数据通过网络接口（IP地址和端口 ）发送到同一台计算机上的不同进程或网络上的另一台计算机。TCP/UDP。

## 线程间的同步(通信) 

线程间通信的主要目的是用于**线程同步**，所以**线程没有进程通信**中用于**数据交换**的通信机制。 

常见的有1.互斥锁；2. 读写锁；3.信号量 等等。但都是基于以下实现

### 1. 临界区

对临界资源进行访问的那段代码称为临界区。

为了互斥访问临界资源，每个进程在进入临界区之前，需要先进行检查。

```c++
do{ 
    进入区(entry section)
  	临界区(critical section)
  	退出区(exit section)
  	剩余区(remainder section)	//其他代码
}while(true);
```

**互斥**：如果进程在临界区，那么其他进程都不能在其临界区

### 2. 信号量

#### 信号量的实现

对于应用开发人员来说，信号量(semaphore)是最基础的同步工具。

信号量S是正数变量，除了初始化外，它只能通过两个标准**原子操作**：`wait()`,`signal()`来访问。

```c++
//wait()定义可表示为：
wait(S)
{
    while(S<=0)
    ;
    S--;
}
//signal()定义可表示为:
signal(S)
{
    S++;
}
```

#### 用法

通常操作系统区分 **计数信号量**（值不受限制）和**二进制信号量**（值只能为0或1）。有的信号量称二进制信号量为**互斥锁**。

```
do{
    wait(mutex);
    //临界区
    signal(mutex);
    //剩余区
}while(true);
```

当然这里的信号量主要缺点是都要要求**忙等待**。这对只有一个处理器为多个进程所共享。忙等待浪费了CPU时钟，这本来可有效地为其他进程所使用，这种类型的信号量也称为**自旋锁(spinlock)**,这时进程在其等待锁时还在运行（即可以在等待锁时不进行上下文切换）。

为了**克服忙等待**需要重新定义信号量操作`wait()`和`signal()`。当一个进程执行`wait()`操作时，发现信号量值不为正，则它必须等待，但是这个等待应该不是忙等待而是**阻塞自己**。具体阻塞操作将一个进程放入到与信号量相关的等待队列中，并将该进程的状态切换成等待状态。接着控制转到CPU调度程序，以选择另一个进程来执行。

```c++
//阻塞自己的信号量
typedef struct
{
    int value;
    struct process* list;
}semaphore
//每个信号量有一个整型值和进程链表。当一个进程必须等待信号量时，就加入到进程链表中。
wait(semaphore* S)
{
    S->value--;
    if(S->value<0){
        add this process to S->list;
        block();
    }
}
signal(semaphore* S)
{
    S->value++;
    if(S->value<=0)
    {
        remove a process P from S->list;
        wakeup(P);
    }
}
//操作block()挂起调用它的进程，操作wakeup(P)重新启动阻塞进程P的执行。这两个操作由系统调用来提供。
```

### 生产者和消费者问题

根据`wait()`,`signal() ` 实现**生产者与消费者**, 信号量mutex初始化为1，信号量empty和full分别表示空缓冲项和满缓冲项的个数。empty初始化为n，full初始化为0。

![producer-consumer.png](https://github.com/Realee007/jobbook/blob/master/src/image/producer-consumer.png?raw=true) 



```c++
#define N 100
typedef int semaphore;
semaphore mutex =1;
semaphore empty =N;
semapgore full = 0;

void producer()
{
    while(true)
    {
        int item = produce_item();
        wait(&empty);
        wait(&mutex);
        add_item(item);
        signal(&mutex);
        signal(&full);
    }
}

void consumer()
{
    while(true)
    {
        wait(&full);
        wait(&mutex);
        int item = remove_item();
        wait(&mutex);
        signal(&empty);
        consume_item(item);
    }
}
```



### 哲学家问题

一个哲学家通过`wait()`试图获取相应的筷子，他会通过`signal()`操作释放相应的筷子.

此时共享数据`semaphore chopstick[5]` 其中所以chopstick的元素初始化为1.哲学家i的结构图如下：

![zhexuejia_problem.png](https://github.com/Realee007/jobbook/blob/master/src/image/zhexuejia_problem.png?raw=true) 

虽然这可以保证没有两个哲学家同时使用同一只筷子。但是这和可能会导致**死锁**即5个哲学家同饥饿且同时拿起左边的筷子。所以筷子的信号量都是0，当哲学家试图拿起右边的筷子时，他永远在等待。

解决死锁的办法就是，要求哲学家在两只筷子均可用时才能拿起筷子。

#### 管程（monitor）

管程结构确保**在一个时刻只能有一个进程使用管程**。进程在无法继续执行的时候不能一直占用管程，否者其它进程永远不能使用管程。

```c++
//使用管程实现哲学家问题
monitor dp
{
	enum{THINKING,HUNGRY,EATING} state[5];
	condition self[5];
	void pickup(int i)
    {
    	state[i]=HUNGRY;
    	test(i);
    	if(state[i]!=EATING)
    		self[i].wait();
    }
    
    void putdown(int i )
    {
    	state[i] = THINKING;
    	test((i+4)%5);
    	test((i+1)%5);
    }
    void test(int i)
    {
    	if( (state[(i+4)%5]!=EATING)&&(state[i]==HUNGRY)&&(state[(i+1)%5]!=EATING) )
        {
        	state[i] = EATING;
        	self[i].signal();
        }
    }
    initialization_code()
    {
    	for(int i =0;i<5;++i)
    		state[i] = THINKING;
    
    }
}
```

## 进程中断

进程的中断是归类于**异常**。在进程运行的过程中，处理器检测到事件（event），并确定异常号，随后触发异常，转到相应的处理程序。异常表的起始地址放在**异常表基址寄存器**（特殊CPU寄存器）。

具体的异常分为四类：

| 类别 | 原因              | 异步/同步 | 返回行为           |
| ---- | ----------------- | --------- | ------------------ |
| 中断 | 来自I/O设备的信号 | 异步      | 返回下一条指令     |
| 陷阱 | 有意的异常        | 同步      | 返回下一条指令     |
| 故障 | 潜在可恢复的错误  | 同步      | 可能返回到当前指令 |
| 终止 | 不可恢复的错误    | 同步      | 不会返回           |

异常的异步和同步理解：

中断有两种，一种是CPU本身在执行程序的过程中产生的，一种是由CPU外部产生的。

1. 外部中断，就是通常所讲的“中断”（interrupt）。对于执行程序来说，这种“中断”的发生完全是异步的，因为不知道什么时候会发生。CPU对其的响应也完全是被动的，可以通过“关中断”指令关闭对其的响应。  
2. 由软件产生的中断一般是由专设的指令，如X86中的“INT n”在程序中有意产生的是主动的，同步的。只要CPU执行一条INT指令，在开始执行下一条指令之前一定会进入中  断服务程序。这种主动的中断称故障指令。  

## 进程饥饿

在操作系统中，多个进程同时运行时，系统需要确定一个资源分配策略给进程，以提高计算机的吞吐量。如CPU调度，有些**调度是不公平的**，如优先级调度算法。某些已就绪的进程因长时间的等待CPU从而发送**饥饿（无期限的阻塞）**。

实例： 多个thread阻塞在同一个mutex上，系统调度器会选择其中一个接触阻塞状态，而选择的方式有可能造成某个不幸的thread永远也不会解除阻塞进入运行状态。

 低优先级进程的饥饿的解决方法之一是**老化（aging）**，即逐渐增加在系统中等待很长时间的进程的优先级。



## 死锁(deadlock)

### 死锁的必要条件

必须同时满足4个条件：

1. 互斥：至少一个资源必须处于非共享模式，即一次只能有一个进程使用。如果另一进程要用则必须等待。
2. 占用并等待：一个进程必须占用至少一个资源并等待另一资源，而该资源被其她进程所占有。
3. 非抢占：资源只能在进程完成任务后自动释放。
4. 循环等待：一组等待进程{P0,P1,....,Pn}，P0等待的资源被P1占有，P1的被P2占有，......，Pn等待的资源被P0所占有。

总而言之，如果资源分配没有环，那么系统就不处于死锁状态。如果有环那么，系统可能会处于死锁状态。

### 处理死锁的方法

#### 1. 死锁预防

确保4个条件有一个不成立。通过限制资源申请的方法来预防死锁，这样副作用是低设备使用率和系统吞吐量。

#### 2. 死锁避免

##### 1.安全状态

![deadlock avoid-safestate.png](https://github.com/Realee007/jobbook/blob/master/src/image/deadlock%20avoid-safestate.png?raw=true) 

图 a 的第二列 Has 表示已拥有的资源数，第三列 Max 表示总共需要的资源数，Free 表示还有可以使用的资源数。从图 a 开始出发，先让 B 拥有所需的所有资源（图 b），运行结束后释放 B，此时 Free 变为 5（图 c）；接着以同样的方式运行 C 和 A，使得所有进程都能成功运行，因此可以称图 a 所示的状态时安全的。

定义：如果没有死锁发生，并且即使所有进程突然请求对资源的最大需求，也仍然存在某种调度次序能够使得每一个进程运行完毕，则称该状态是安全的。

安全状态的检测与死锁的检测类似，因为**安全状态必须要求不能发生死锁**。下面的银行家算法与死锁检测算法非常类似，可以结合着做参考对比。



##### 2.单个资源的银行家算法

一个小城镇的银行家，他向一群客户分别承诺了一定的贷款额度，算法要做的是判断对请求的满足是否会进入不安全状态，如果是，就拒绝请求；否则予以分配。 

![deadlock avoid-bank1.png](https://github.com/Realee007/jobbook/blob/master/src/image/deadlock%20avoid-bank1.png?raw=true)

上图 c 为不安全状态，因此算法会拒绝之前的请求，从而避免进入图 c 中的状态。  

##### 3.多个资源的银行家算法

![deadlock avoid-bank2.png](https://github.com/Realee007/jobbook/blob/master/src/image/deadlock%20avoid-bank2.png?raw=true) 

上图中有五个进程，四个资源。左边的图表示已经分配的资源，右边的图表示还需要分配的资源。最右边的 E、P 以及 A 分别表示：总资源、已分配资源以及可用资源，注意这三个为向量，而不是具体数值，例如 A=(1020)，表示 4 个资源分别还剩下 1/0/2/0。

检查一个状态是否安全的算法如下：

- 查找右边的矩阵是否存在一行小于等于向量 A。如果不存在这样的行，那么系统将会发生死锁，状态是不安全的。
- 假若找到这样一行，将该进程标记为终止，并将其已分配资源加到 A 中。
- 重复以上两步，直到所有进程都标记为终止，则状态时安全的。

如果一个状态不是安全的，需要拒绝进入这个状态。

#### 3. 死锁恢复

- 利用抢占恢复
- 利用回滚恢复
- 通过杀死进程恢复





## 其他：

### 线程池

线程池是在进程开始时，创建一定数量的线程，并放入到池中以等待工作，当服务器收到请求时会唤醒池中的线程（如果可用的话），并将要处理的请求传递给它。一旦完成了服务，它会返回到池中再等待。如果池中没有可用的线程，那么服务器会一直等待直到有空线程为止。

C++封装线程池

[C++封装线程池参考](https://blog.csdn.net/gavingreenson/article/details/72770818)

对象池用来通常用来避免重复的内存分配和销毁及内存碎片的产生.

适用的情况如下：

1. 当你需要频繁地创建和销毁对象时
2. 对象的大小类似
3. 堆内存的分配有可能造成内存碎片时
4. 每个对象有封装一些自己的资源类似数据库或网络连接，很难获取及复用

[unity3d 飞机子弹模型 使用线程池](https://blog.csdn.net/l773575310/article/details/71601460)



# 内存管理

CPU所生成的地址称为：虚拟地址（或逻辑地址），而内存单元所看到的地址称为物理地址。



## 连续内存分配

多个进程执行时，需要为调入内存的进程分配内存空间，连续内存分配指为每个进程一个连续的内存区域。

缺点：随着进程的装入和移出，空闲的内存空间被分为小片段。虽然可用内存之和满足所需，但内存并不连续。这就出现了外部**碎片**的问题。

## 分页

分页是将物理内存分为固定大小的快，称为帧(frame)；而将虚拟内存分为同样大小的块，称为页(page)。

内存管理单元（MMU）管理着地址空间和物理内存的转换，其中的页表（Page table）存储着页（程序地址空间）和页框（物理内存空间）的映射表。

由CPU生成的**虚拟地址**=**页号**+**页偏移**

下图的页表存放着 16 个页，这 16 个页需要用 4 个比特位来进行索引定位，也就是存储页号，剩下 12 个比特位存储偏移量。

![page_pageindex.png](https://github.com/Realee007/jobbook/blob/master/src/image/page_pageindex.png?raw=true) 

例如对于虚拟地址（0010 000000000100），前 4 位是存储页号 2，读取表项内容为（110 1）。该页在内存中，并且页框的地址为 （110 000000000100。



## 分段

采用分页内存管理有一个不可避免的问题：用户视角的内存和实际物理内存的分离。

分段就是支持用户视角的内存管理方案。**虚拟地址= 段名称+偏移量**。



下图为一个编译器在编译过程中建s立的多个表，有 4 个表是动态增长的，如果使用分页系统的一维地址空间，动态增长的特点会导致覆盖问题的出现。 

![page_problem.png](https://github.com/Realee007/jobbook/blob/master/src/image/page_problem.png?raw=true) 

分段的做法是把每个表分成段，一个段构成一个独立的地址空间。每个段的长度可以不同，并且可以动态增长。 

![segment.png](https://github.com/Realee007/jobbook/blob/master/src/image/segment.png?raw=true) 

## 分页与分段的比较

- 对程序员的透明性：分页透明，但是分段需要程序员显示划分每个段。
- 地址空间的维度：分页是一维地址空间，分段是二维的。
- 大小是否可以改变：页的大小不可变，段的大小可以动态改变。
- 出现的原因：分页主要用于实现虚拟内存，从而获得更大的地址空间；分段主要是为了使程序和数据可以被划分为逻辑上独立的地址空间并且有助于共享和保护。



## 虚拟内存

虚拟内存的目的：

1. 将逻辑内存与物理内存分开，可以让物理内存扩充成更大的逻辑内存，使得程序员不用担心可用的有限物理内存空间。
2. 通过将共享对象映射到虚拟地址空间，系统库可以为多个进程所共享。
3. 虚拟内存允许进程共享内存，即多个进程之间可以用个使用共享内存来通信。

为了更好的管理内存，操作系统将内存抽象成地址空间。每个程序拥有自己的地址空间，这个地址空间被分割成多个块，每一块称为一页。这些页被映射到物理内存，但不需要映射到连续的物理内存，也不需要所有页都必须在物理内存中。当程序引用到不在物理内存中的页时，由硬件执行必要的映射，将缺失的部分装入物理内存并重新执行失败的指令。

从上面的描述中可以看出，虚拟内存允许程序不用将地址空间中的每一页都映射到物理内存，也就是说一个程序不需要全部调入内存就可以运行，这使得有限的内存运行大程序称为可能。例如有一台计算机可以产生 16 位地址，那么一个程序的地址空间范围是 0~64K。该计算机只有 32KB 的物理内存，虚拟内存技术允许该计算机运行一个 64K 大小的程序。

### 页面置换算法

在程序运行过程中，如果要访问的页面不在内存中，就发生缺页中断从而将该页调入内存中。此时如果内存已无空闲空间，系统必须从内存中调出一个页面到磁盘对换区中来腾出空间。

页面置换算法和缓存淘汰策略类似，可以将内存看成磁盘的缓存。在缓存系统中，缓存的大小有限，当有新的缓存到达时，需要淘汰一部分已经存在的缓存，这样才有空间存放新的缓存数据。

页面置换算法的主要目标是使**页面置换频率最低**（也可以说**缺页率最低**）。

#### 先进先出(FIFO）

选择换出的页面是最先进入的页面。 

![FIFO-page.png](https://github.com/Realee007/jobbook/blob/master/src/image/FIFO-page.png?raw=true) 

#### 最优置换(optimal)

所选择的被换出的页面将是最长时间内不再被访问，通常可以保证获得最低的缺页率。

是一种理论上的算法，因为无法知道一个页面多长时间不再被访问。

举例：一个系统为某进程分配了三个物理块，并有如下页面引用序列：

![optimal-page-replace.png](https://github.com/Realee007/jobbook/blob/master/src/image/optimal-page-replace.png?raw=true) 

开始运行时，先将 7, 0, 1 三个页面装入内存。当进程要访问页面 2 时，产生缺页中断，会将页面 7 换出，因为页面 7 再次被访问的时间最靠后。

#### 最近最少使用(LRU) 

虽然无法知道将来要使用的页面情况，但是可以知道过去使用页面的情况。LRU 将最近最久未使用的页面换出。

![LRU-page.png](https://github.com/Realee007/jobbook/blob/master/src/image/LRU-page.png?raw=true) 

##### LRU的实现

为了实现 LRU，需要在内存中维护一个所有页面的链表。当一个页面被访问时，将这个页面移到链表表头。这样就能保证链表表尾的页面时最近最久未访问的。

> Design an LRU cache with all the operations to be done in O(1) . 

使用双向链表+哈希表

具体操作即STL中`list`+`unordered_map`. 

1. 插入:当cache未满时，新数据插入到头部。
2. 替换：当cache已满时，将新数据插入到头部，并删除掉尾部节点。
3. 查找：每次数据被查询到时，将此数据移动到链表头部。



# 链接

## 编译系统

以下是一个 hello.c 程序：

```
#include <stdio.h>

int main()
{
    printf("hello, world\n");
    return 0;
}
```

在 Unix 系统上，由编译器把源文件转换为目标文件。

```
gcc -o hello hello.c
```

这个过程大致如下：

![compling.jpg](https://github.com/Realee007/jobbook/blob/master/src/image/compling.jpg?raw=true) 

- 预处理阶段：处理以 # 开头的预处理命令；
- 编译阶段：翻译成汇编文件；(词法分析、语法分析、语义分析及优化而生成相应的汇编代码文件)
- 汇编阶段：通过**汇编器**将汇编文件翻译成可重定向目标文件(Object file)；
- 链接阶段：将可重定向目标文件和 printf.o 等单独预编译好的目标文件进行合并，得到最终的可执行目标文件。

## 静态链接

静态连接器以一组可重定向目标文件为输入，生成一个完全链接的可执行目标文件作为输出。链接器主要完成以下两个任务：

- 符号解析：每个符号对应于一个函数、一个全局变量或一个静态变量，符号解析的目的是将每个符号引用与一个符号定义关联起来。
- 重定位：链接器通过把每个符号定义与一个内存位置关联起来，然后修改所有对这些符号的引用，使得它们指向这个内存位置。

![static linking.jpg](https://github.com/Realee007/jobbook/blob/master/src/image/static%20linking.jpg?raw=true) 

## 目标文件

- 可执行目标文件：可以直接在内存中执行；
- 可重定向目标文件：可与其它可重定向目标文件在链接阶段合并，创建一个可执行目标文件；
- 共享目标文件：这是一种特殊的可重定向目标文件，可以在运行时被动态加载进内存并链接；

## 动态链接

静态库有以下两个问题：

- 当静态库更新时那么整个程序都要重新进行链接；
- 对于 printf 这种标准函数库，如果每个程序都要有代码，这会极大浪费资源。

共享库是为了解决静态库的这两个问题而设计的，在 Linux 系统中通常用 .so 后缀来表示，Windows 系统上它们被称为 DLL。它具有以下特点：

- 在给定的文件系统中一个库只有一个文件，所有引用该库的可执行目标文件都共享这个文件，它不会被复制到引用它的可执行文件中；
- 在内存中，一个共享库的 .text 节（已编译程序的机器代码）的一个副本可以被不同的正在运行的进程共享。

![dynamic linking.jpg](https://github.com/Realee007/jobbook/blob/master/src/image/dynamic%20linking.jpg?raw=true) 

# 部分词语解释

# 参考

《操作系统概念(第七版)》 Abranham Silberschatz 翻译版  郑扣根(译)

《operating system concepts (9th) 》 Abranham Silberschatz