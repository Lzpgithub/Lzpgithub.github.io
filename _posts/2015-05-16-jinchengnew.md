---
layout: post
title: 进程的创建
categories: 操作系统
description: 进程创建
keywords: C, 进程创建
---

#### 进程的创建

进程的创建方式有以下两种：

(1).由系统程序模块统一创建。

(2).由父进程创建，父进程创建子进程以完成并行工作。

由系统创建的进程之间的关系是平等，它们之间一般不存在资源继承关系。而在父进程创建的进程之间则存在隶属关系，且互相构成树型结构的家族关系，属于某个家族的一个进程可以继承其父进程所拥有的资源。

无论是系统创建方式还是父进程创建方式，都必须调用创建原语来实现。创建原语扫描系统PCB链表，在找到一定PCB表之后，填入调用者提供的有关参数，最后形成代表进程的PCB结构，这些参数包括：进程名、进程优先级P0、进程正文段起始地址d0、资源清单R0等。

![](/images/posts/OS/8.png)

#### 进程的撤销

进程撤销有三种情况：该进程已完成所要求的功能而正常终止；由于某种错误导致非正常终止；祖先进程要求撤销某个子进程。

进程被撤销时，进程都必须释放它所占用的各种资源和PCB结构本身，以利于资源的有效利用。当然，一个进程所占有的某些资源在使用结束时可能早已释放。

当一个祖先进程撤销某个子进程时，还需审查该子进程是否还有自己的子孙进程，若有的话，还需要撤销其子孙进程的PCB结构和释放它们所占有的资源。

撤销原语首先检查PCB进程链或者进程家族，寻找所要撤销的进程是否存在，如果找到了所要撤销的的进程PCB结构，则撤销原语释放该进程的所占有的资源之后，把对应的PCB结构从进程链或进程家族中摘下并返回给PCB空队列。如果被撤销进程有自己的子进程，则撤销原语先撤销其子进程的PCB结构并释放子进程所占有的资源之后，再撤销当前进程的PCB结构和释放其资源。

![](/images/posts/OS/9.png)

#### 进程的阻塞与唤醒

实现进程的执行状态到等待状态，又由等待状态到就绪状态的转换对应于两种原语：阻塞原语和唤醒原语。

阻塞原语在一个进程期待某一事件(例如键盘输入数据、写盘、其他进程发来的数据等)发生，但发生条件尚不具备时，该进程就调用阻塞原语来阻塞自己。阻塞原语在阻塞一个进程时，由于该进程正处于执行状态，因此先中断处理机和保存该进程的CPU现场，然后将被阻塞进程置阻塞状态后插入等待队列中，再通过转进程调度程序选择新的就绪进程投入运行。转进程调度程序是很重要的，否则，处理机将会出现空转而浪费资源。

![](/images/posts/OS/10.png)

当等待队列中的进程所等待的事件发生时，等待该事件的所有进程都将被唤醒。一个处于阻塞状态的进程不可能自己唤醒自己，唤醒一个进程有两种方法：首先是系统进程唤醒，系统进程统一控制事件的发生并将"事件发生"这一消息通知等待进程，从而使得该进程因等待事件已发生而进入就绪队列；另一个是事件发生进程唤醒，事件发生进程和被唤醒进程之间是合作关系。

唤醒原语既可被系统进程调用，也可以被事件发生进程调用，因此称调用唤醒原语的进程为唤醒进程。唤醒原语首先将被唤醒进程从相应的等待队列中摘下，将被唤醒进程设置为就绪状态之后，送入就绪队列。

![](/images/posts/OS/11.png)


#### 在Linux环境下创建进程fork()函数

##### fork()函数基础

 一个进程，包括代码、数据和分配给进程的资源。**fork()函数通过系统调用创建一个与原来进程几乎完全相同的进程，也就是两个进程可以做完全相同的事**，但如果初始参数或者传入的变量不同，两个进程也可以做不同的事。

一个进程调用fork()函数后，**系统先给新的进程分配资源，例如存储数据和代码的空间。然后把原来的进程的所有值都复制到新的新进程中，只有少数值与原来的进程的值不同，相当于克隆了一个自己**。

例如：

```cpp
#include <unistd.h>  
#include <stdio.h>   
int main ()   
{   
    pid_t fpid;                      //fpid表示fork函数返回的值  
    int count=0;  

    fpid=fork();                     //开始创建子进程 和父进程一模一样

    if (fpid < 0)                    //创建进程出错
        printf("error in fork!");    
    else if (fpid == 0){             //fpid等于0 代表在子进程中  getpid函数为取得进程ID
        printf("i am the child process, my process id is %d/n",getpid());   
        printf("我是爹的儿子/n");        
        count++;  
    }else{                           //fpid大于0 代表咱父进程中
        printf("i am the parent process, my process id is %d/n",getpid());   
        printf("我是孩子他爹/n");  
        count++;  
    }  
    printf("统计结果是: %d/n",count);  
    return 0;  
}  
```

程序运行结果为：

```cpp
i am the child process, my process id is 5574
我是爹的儿子
统计结果是: 1

i am the parent process, my process id is 5573
我是孩子他爹
统计结果是: 1
```


在语句fpid=fork()之前，**只有一个进程在执行这段代码，但在这条语句之后，就变成两个进程在执行了，这两个进程的几乎完全相同**，将要执行的下一条语句都是if(fpid<0)....，为什么两个进程的fpid不同呢，这与fork函数的特性有关。**fork调用的一个奇妙之处就是它仅仅被调用一次，却能够返回两次，它可能有三种不同的返回值**：

1）在父进程中，fork返回新创建子进程的进程ID；

2）在子进程中，fork返回0；

3）如果出现错误，fork返回一个负值；

在fork函数执行完毕后，如果创建新进程成功，则出现两个进程，一个是子进程，一个是父进程。在子进程中，fork函数返回0，在父进程中，fork返回新创建子进程的进程ID。**我们可以通过fork返回的值来判断当前进程是子进程还是父进程**。

为什么在父子进程中不同？其实就相当于链表，进程形成了链表，父进程的fpid(p意味point)指向子进程的进程id, 因为子进程没有子进程，所以其fpid为0。

fork出错可能有两种原因：
1）当前的进程数已经达到了系统规定的上限，这时errno的值被设置为EAGAIN。

2）系统内存不足，这时errno的值被设置为ENOMEM。

创建新进程成功后，系统中出现两个基本完全相同的进程，这两个进程执行没有固定的先后顺序，哪个进程先执行要看系统的进程调度策略。每个进程都有一个独特（互不相同）的进程标识符（process ID），可以通过getpid()函数获得，还有一个记录父进程pid的变量，可以通过getppid()函数获得变量的值。

fork执行完毕后，出现两个进程：

![](/images/posts/OS/27.png)

两个进程的内容完全一样啊，怎么打印的结果不一样啊？那是因为判断条件的原因，上面列举的只是进程的代码和指令，还有变量。执行完fork后，进程1的变量为count=0，fpid！=0（父进程）。进程2的变量为count=0，fpid=0（子进程），这两个进程的变量都是独立的，存在不同的地址中，不是共用的，这点要注意。可以说，我们就是通过fpid来识别和操作父子进程的。还有人可能疑惑为什么不是从#include处开始复制代码的，这是因为fork是把进程当前的情况(相关堆栈、寄存器、程序指针)拷贝一份，执行fork时，进程已经执行完了int count=0;fork只拷贝下一个要执行的代码到新的进程。


##### fork函数进阶应用

```cpp
#include <unistd.h>  
#include <stdio.h>  
int main(void)  
{  
   int i=0;  
   printf("i son/pa ppid pid fpid/n");  
   //ppid指当前进程的父进程pid  
   //pid指当前进程的pid,  
   //fpid指fork返回给当前进程的值  
   for(i=0;i<2;i++){  
       pid_t fpid=fork();  
       if(fpid==0)  
           printf("%d child  %4d %4d %4d/n",i,getppid(),getpid(),fpid);  
       else  
           printf("%d parent %4d %4d %4d/n",i,getppid(),getpid(),fpid);  
   }  
   return 0;  
}  
```

运行结果：

```cpp
i   son/pa   ppid   pid    fpid
0   parent   2043   3224   3225
0   child    3224   3225    0
1   parent   2043   3224   3226
1   parent   3224   3225   3227
1   child     1     3227    0
1   child     1     3226    0 
```

分析：

第一步：在父进程中，指令执行到for循环中，i=0，接着执行fork，fork执行完后，系统中出现两个进程，分别是p3224和p3225（后面我都用pxxxx表示进程id为xxxx的进程）。可以看到父进程p3224的父进程是p2043，子进程p3225的父进程正好是p3224。我们用一个链表来表示这个关系：

```cpp
p2043->p3224->p3225 
```

第一次fork后，p3224（父进程）的变量为i=0，fpid=3225（fork函数在父进程中返向子进程id），代码内容为：

```cpp
for(i=0;i<2;i++){  
    pid_t fpid=fork();                   //执行完毕，i=0，fpid=3225  
    if(fpid==0)  
       printf("%d child  %4d %4d %4d/n",i,getppid(),getpid(),fpid);  
    else  
       printf("%d parent %4d %4d %4d/n",i,getppid(),getpid(),fpid);  
}  
return 0;  
```

p3225（子进程）的变量为i=0，fpid=0（fork函数在子进程中返回0），代码内容为：

```cpp
for(i=0;i<2;i++){  
    pid_t fpid=fork();                   //执行完毕，i=0，fpid=0  
    if(fpid==0)  
       printf("%d child  %4d %4d %4d/n",i,getppid(),getpid(),fpid);  
    else  
       printf("%d parent %4d %4d %4d/n",i,getppid(),getpid(),fpid);  
}  
return 0;
```

因此打印结果为：

```cpp
0   parent   2043   3224   3225
0   child    3224   3225    0
```

第二步：假设父进程p3224先执行，当进入下一个循环时，i=1，接着执行fork，系统中又新增一个进程p3226，对于此时的父进程，p2043->p3224（当前进程）->p3226（被创建的子进程）。对于子进程p3225，执行完第一次循环后，i=1，接着执行fork，系统中新增一个进程p3227，对于此进程，p3224->p3225（当前进程）->p3227（被创建的子进程）。从输出可以看到p3225原来是p3224的子进程，现在变成p3227的父进程。父子是相对的，这个大家应该容易理解。只要当前进程执行了fork，该进程就变成了父进程了，就打印出了parent。

所以打印出结果是：

```cpp
1 parent 2043 3224 3226
1 parent 3224 3225 3227
```

第三步：第二步创建了两个进程p3226，p3227，这两个进程执行完printf函数后就结束了，因为这两个进程无法进入第三次循环，无法fork，该执行return 0;了，其他进程也是如此。

以下是p3226，p3227打印出的结果：

```cpp
1 child     1 3227    0
1 child     1 3226    0 
```

细心的读者可能注意到p3226，p3227的父进程难道不该是p3224和p3225吗，怎么会是1呢？这里得讲到进程的创建和死亡的过程，**在p3224和p3225执行完第二个循环后，main函数就该退出了，也即进程该死亡了，因为它已经做完所有事情了**。p3224和p3225死亡后，p3226，p3227就没有父进程了，这在操作系统是不被允许的，所以p3226，p3227的父进程就被置为p1了，p1是永远不会死亡的。

总结一下，这个程序执行的流程如下：

![](/images/posts/OS/28.png)

这个程序最终产生了3个子进程，执行过6次printf()函数。


```cpp
#include <unistd.h>  
#include <stdio.h>  
int main(void)  
{  
   int i=0;  
   for(i=0;i<3;i++){  
       pid_t fpid=fork();  
       if(fpid==0)  
           printf("son/n");  
       else  
           printf("father/n");  
   }  
   return 0;
}  
```

运行结果为：

```cpp
father
son
father
father
father
father
son
son
father
son
son
son
father
son 
```

总结一下规律，对于这种N次循环的情况，执行printf函数的次数为2*（1+2+4+...+2N-1）次，创建的子进程数为1+2+4+...+2N-1个。如果想测一下一个程序中到底创建了几个子进程，最好的方法就是调用printf函数打印该进程的pid，也即调用printf("%d/n",getpid());或者通过printf("+/n");来判断产生了几个进程。


```cpp
#include <unistd.h>  
#include <stdio.h>  
int main() {  
    pid_t fpid;//fpid表示fork函数返回的值  
    //printf("fork!");  
    printf("fork!/n");  
    fpid = fork();  
    if (fpid < 0)  
        printf("error in fork!");  
    else if (fpid == 0)  
        printf("I am the child process, my process id is %d/n", getpid());  
    else  
        printf("I am the parent process, my process id is %d/n", getpid());  
    return 0;  
}  
```

执行结果如下：

```cpp
fork!
I am the parent process, my process id is 3361
I am the child process, my process id is 3362 
```

如果把语句printf("fork!/n");注释掉，执行printf("fork!");则新的程序的执行结果是：

```cpp
fork!I am the parent process, my process id is 3298
fork!I am the child process, my process id is 3299 
```

程序的唯一的区别就在于一个/n回车符号，为什么结果会相差这么大呢？

这就跟printf的缓冲机制有关了，printf某些内容时，操作系统仅仅是把该内容放到了stdout的缓冲队列里了,并没有实际的写到屏幕上。但是,只要看到有/n 则会立即刷新stdout,因此就马上能够打印了。运行了printf("fork!")后,“fork!”仅仅被放到了缓冲里,程序运行到fork时缓冲里面的“fork!”被子进程复制过去了。因此在子进程度stdout缓冲里面就也有了fork! 。所以,你最终看到的会是fork!  被printf了2次！！！！而运行printf("fork! /n")后,“fork!”被立即打印到了屏幕上,之后fork到的子进程里的stdout缓冲里不会有fork! 内容。因此你看到的结果会是fork! 被printf了1次！！！！


```cpp
#include <stdio.h>  
#include <unistd.h>  
int main(int argc, char* argv[])  
{  
   fork();  
   fork() && fork() || fork();  
   fork();  
   return 0;  
}  
```

问题是不算main这个进程自身，程序到底创建了多少个进程。

为了解答这个问题，我们先做一下弊，先用程序验证一下，到此有多少个进程。

```cpp
#include <stdio.h>  
int main(int argc, char* argv[])  
{  
   fork();  
   fork() && fork() || fork();  
   fork();  
   printf("+/n");  
}  
```

```cpp
答案是总共20个进程，除去main进程，还有19个进程。
我们再来仔细分析一下，为什么是还有19个进程。第一个fork和最后一个fork肯定是会执行的。
主要在中间3个fork上，可以画一个图进行描述。这里就需要注意&&和||运算符。
A&&B，如果A=0，就没有必要继续执行&&B了；
A非0，就需要继续执行&&B。A||B，如果A非0，就没有必要继续执行||B了，A=0，就需要继续执行||B。
fork()对于父进程和子进程的返回值是不同的，按照上面的A&&B和A||B的分支进行画图，可以得出5个分支。
```

![](/images/posts/OS/29.png)

加上前面的fork和最后的fork，总共4*5=20个进程，除去main主进程，就是19个进程了。