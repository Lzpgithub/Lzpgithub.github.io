---
layout: post
title: 进程的死锁
categories: 操作系统
description: 进程死锁
keywords: C, 进程死锁
---

#### 进程死锁的定义

各进程在使用系统资源时，应注意系统产生死锁的问题。所谓死锁，**是指各并发进程彼此互相等待对方所拥有的资源，且这些并发进程在得到对方的资源之前不会释放自己所拥有的资源**。

![](/images/posts/OS/38.png)

例子：生产者和消费者来看进程死锁

设生产者进程已获得对缓冲区队列的操作权，生产者进程进一步要求对缓冲区的某一空缓冲区进行置入消息操作。然而，此时缓冲区队列内所有缓冲区都是满的，即只有消费者进程才能对它们进行取消息操作。因此生产者进程进入等待状态。反过来，消费者进程拥有对各缓冲区操作的操作权，为了对各缓冲区进程操作，它要申请对缓冲区队列操作的操作权。由于缓冲队列的操作权被生产者掌握，且生产者进程不会自动释放它，从而导致消费者进程也进入等待状态而陷入死锁。


#### 死锁产生原因

死锁的起因是并发进程的资源竞争。死锁产生的根本原因是在于系统提供的资源个数少于并发进程所求的该类资源数。显然，由于资源的有限性，不可能为所有要求资源的进程无限制地提供资源。但是可以采用适当的资源分配算法，以达到消除死锁的目的。

进程死锁产生的4个必要条件：

1.互斥条件。并发进程所要求和占有资源是不能同时被两个以上的进程使用或操作的，进程对它所需的资源进行排他性控制。

2.不可剥夺条件。进程所获得的资源在未使用完毕之前，不能被其他进程强行剥夺，只能由获得该资源的进程自己释放。

3.部分分配。进程每次申请它所需要的一部分资源，在等待新资源的同时，继续占用已分配得到的资源。

4.环路条件。存在一种进程循环链，链中每一个进程已获得的资源同时被下一个进程所请求。

显然，只要上述4个必要条件的某一个不满足，则死锁就可以排除。


#### 死锁的排除方法

解决死锁的方法一般可分为预防、避免、检测与恢复等3种。**预防**是采用某种策略，限制并发进程对资源的请求，从而根据使得死锁的必要条件在系统执行的任何时间都不满足。**避免**是指系统在分配资源时，根据资源的使用情况提前做出预测，从而避免死锁的发生。**检测与恢复**是指系统设有专门的机构，当死锁发生时，该机构能够检测到死锁的发生位置和原因，并能通过外力破坏死锁发生的必要条件，从而使得并发进程从死锁状态中恢复出来。

一般而言，由于操作系统的并行与共享以及随机性的特点，通过预防和避免的手段达到排除死锁的目的是一件非常困难的事情，这需要较大的系统开销，甚至不能充分利用资源。死锁的检测和恢复则与此相反，不必花费多少执行时间就能发现死锁和从死锁中恢复出来。因此，在实际操作系统中大都使用检测和恢复排除死锁。

##### 死锁预防

一种方法是打破资源的互斥和不可剥夺这两个条件。例如允许进程同时访某些资源等。然而，这种方法不能解决访问那些不允许被同时访问的资源时带来的死锁问题，比如打印机等。另一种方法则是打破资源的部分分配这个死锁产生的必要条件。即预先分配各并发进程所需要的全部资源。如某个进程的资源得不到满足时，则安排一定的等待次序让其他进程释放资源。但是这种方法会有如下确定：

(1).在许多情况下，一个进程在执行之前不可能提出它所需要的全部资源。

(2).无论所需资源何时用到，一个进程只有在所有要求资源都得到满足之后才开始执行。

(3).对于那些不经常使用的资源，进程在生存过程期间一直占用它们是一种极大的浪费。

(4).降低了进程的并发性。


##### 死锁避免

死锁避免可被称为动态预防，因为系统采用动态分配资源，在分配过程中预测出死锁发生的可能性并加以避免的方法。死锁避免的一种基本模式是把进程分为多个步骤，其中每个步骤所使用的资源是固定的，且在每一个步骤内，进程所保持的资源数不变，即进程的资源请求、使用与释放要依靠不同的步骤完成。

##### 死锁的检测和恢复

当进程进行资源请求时死锁检测算法检查并发进程组是否构成资源的请求和保持环路。有限状态转移图和petriNet等技术都可以用来有效地判断死锁的发生。死锁的恢复办法较多，最简单的办法是终止各锁住进程，或按一定的顺序终止进程序列，直至已释放到有足够的资源来完成剩下的进程时为止。另外，也可以从被锁住进程强迫剥夺资源以解除死锁。




