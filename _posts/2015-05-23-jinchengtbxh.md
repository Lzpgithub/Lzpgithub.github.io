---
layout: post
title: 信号量实现进程同步
categories: 操作系统
description: 信号量实现进程同步
keywords: C, 进程，同步，信号量
---

#### 公用信号量和私用信号量

用wait(消息名)与signal(消息名)的方式，描述了进程同步的一种实现方法。而信号量的方法也可实现进程同步。一般来说，也可以把各进程之间发送的消息作为信号量看待。

与进程互斥不同的是，这里的信号量只与制约进程和被制约进程有关而不是与整组并发进程有关。因此称该为私用信号量。一个进程P的私有信号量semi是从制约进程发送来的进程P执行条件所需要的信息。与私用信号量相对应，称进程互斥时使用的信号量为公用信号量。

#### 用P,V原语实现进程同步

首先为各并发进程设置私用信号量，然后为各私用信号量赋初值，最后利用P，V原语和私用信号量规定各进程的执行顺序。

##### 发送和接收进程

设进程Pa和Pb通过缓冲区队列传递数据，Pa为发送进程，把数据发送到缓冲区，Pb为接收进程，把数据从缓冲区取出。Pa发送数据时调用过程deposit(data)，Pb接收数据时调用过程remove(data)。且数据的发送和接收过程满足如下条件：

(1).在Pa至少送一块数据入缓冲区之前，Pb不可能从缓冲区中取出数据(假定数据块长等于缓冲区长度)；

(2).Pa往缓冲区队列发送数据时，至少有一个缓冲区是空的；

(3).由Pa发送数据块在缓冲区队列中按先进先出(FIFO)方式排列；

![](/images/posts/OS/25.png)

由题意可知，进程Pa调用的过程deposit(data)和进程Pb调用的过程remove(data)必须同步执行，因为缓冲队列为空时，过程deposit(data)的执行结果是过程remove(data)的执行条件，而当缓冲队列全部装满数据时，remove(data)的执行结果又是deposit(data)的执行条件，满足同步定义。按以下三个步骤描述过程deposit(data)和remove(data)：

(1).设Bufempty为进程Pa的私用信号量，Buffull为进程Pb的私用信号量；

(2).令Bufempty的初始值为n(n为缓冲队列的缓冲区个数)，Buffull的初始值为0；

描述：

```cpp
Pa: deposit(data):
	begin local x
		P(Bufempty);  //Bufempty信号量减1
		按FIFO方式选择一个空缓冲区Buf(x);
		Buf(x)<-dat
		Buf(x)置满标记
		V(Buffull)    //Buffull信号量加1
	end
	
Pb: remove(data):
	begin local x
		P(Buffull);   //Buffull信号量减1
		按FIFO方式选择一个装满数据的缓冲区Buf(x)
		dat<-Buf(x)
		Buf(x)置空标记
		V(Bufempty)   //Bufempty信号量加1
	end
```

局部变量x用来指明缓冲区的区号，给Buf(x)置标志位是为了便于区别和搜索空缓冲区及非空缓冲区。


##### 生产者-消费者问题

把并发进程的同步和互斥问题一般化，可以得到一个抽象的一般模型，即生产者-消费者问题。计算机系统中，每个进程都申请使用和释放各种不同类型的资源，这些资源既可以是像外设、内存及缓冲区等硬件资源，也可以是临界区、数据、例程等软件资源。把系统中使用某一类资源的进程称为该资源的消费者，而把释放同类资源的进程称为该资源的生产者。比如计算进程Pc与打印进程Pp公用一个缓冲区，计算进程Pc把数据送入缓冲区，打印进程Pb从缓冲区中取出数据打印输出，因此，Pc进程相当于数据资源的生产者，而Pp进程相当于消费者。

![](/images/posts/OS/26.png)

设生产者进程和消费者进程时互相等效的，其中,各生产者进程使用的过程deposit(data)和各消费者使用的过程remove(data)可描述如下：

(1).消费者向接收数据时，有界缓冲区中至少有一个单元是满的；

(2).生产者想发送数据时，有界缓冲区中至少有一个单元是空的；

另外，由于有界缓冲区是临界资源，因此，各生产者进程和各消费者进程之间必须互斥执行。

设公用信号量mutex保证生产者进程和消费者进程之间的互斥，设信号量avail为生产者进程的私用信号量，信号full为消费者进程的私用信号量。信号量avail表示有界缓冲区中空单元数，初值为n；信号量full表示有界缓冲区中非空单元数，初值为0。信号量mutex表示可用有界缓冲区的个数，初值为1。从而有：

```cpp
deposit(data):
	begin
		P(avail)    //avail信号量减1
		P(mutex)    //占用缓冲区(互斥)
		送数据入缓冲区某单元
		V(full)     //full信号量加1
		V(mutex)    //释放缓冲区(互斥)
	end

remove(data):
	begin
		P(full)
		P(mutex)
		取缓冲区中某单元数据
		V(avail)
		V(mutex)
	end
```

由于一个过程中包含有几个公用、私用信号量，因此，P，V原语的操作次序要非常小心。一般来说，由于V原语是释放资源的，所以可以任意次序出现，但P原语则不然，但次序混乱，将会造成进程之间的死锁。