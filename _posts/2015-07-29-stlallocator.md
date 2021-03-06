---
layout: post
title:  STL内存管理器的分配策略
categories: C和C++基础
description: STL内存管理器的分配策略
keywords: C, STL内存管理器的分配策略
---

STL提供了很多泛型容器，如vector，list和map。程序员在使用这些容器时只需关心何时往容器内塞对象，而不用关心如何管理内存，需要用多少内存，这些STL容器极大地方便了C++程序的编写。例如可以通过以下语句创建一个vector，它实际上是一个按需增长的动态数组，其每个元素的类型为int整型：

```cpp
vector<int> array;
```

拥有这样一个动态数组后，用户只需要调用push_back方法往里面添加对象，而不需要考虑需要多少内存：

```cpp
array.push_back(10); 
array.push_back(2);
```

**vector会根据需要自动增长内存，在array退出其作用域时也会自动销毁占有的内存，这些对于用户来说是透明的，stl容器巧妙的避开了繁琐且易出错的内存管理工作。**

**隐藏在这些容器后的内存管理工作是通过STL提供的一个默认的allocator实现的。当然，用户也可以定制自己的allocator，只要实现allocator模板所定义的接口方法即可，然后通过将自定义的allocator作为模板参数传递给STL容器，创建一个使用自定义allocator的STL容器对象，如：**

```cpp
vector<int, UserDefinedAllocator> array;
```

大多数情况下，STL默认的allocator就已经足够了。**这个allocator是一个由两级分配器构成的内存管理器，当申请的内存空间大于128byte时，就启动第一级分配器通过malloc直接向系统的堆空间分配，如果申请的内存空间小于128byte时，就启动第二级分配器，从一个预先分配好的内存池中取一块内存交付给用户，这个内存池由16个不同大小（8的倍数，8~128byte）的空闲列表组成，allocator会根据申请内存的大小（将这个大小round up成8的倍数）从对应的空闲块列表取表头块给用户。**


这种做法有两个优点：

**1）小对象的快速分配。**小对象是从内存池分配的，这个内存池是系统调用一次malloc分配一块足够大的区域给程序备用，当内存池耗尽时再向系统申请一块新的区域，整个过程类似于批发和零售，起先是由allocator向总经商批发一定量的货物，然后零售给用户，与每次都总经商要一个货物再零售给用户的过程相比，显然是快捷了。当然，这里的一个问题时，内存池会带来一些内存的浪费，比如当只需分配一个小对象时，为了这个小对象可能要申请一大块的内存池，但这个浪费还是值得的，况且这种情况在实际应用中也并不多见。

**2）避免了内存碎片的生成。**程序中的小对象的分配极易造成内存碎片，给操作系统的内存管理带来了很大压力，系统中碎片的增多不但会影响内存分配的速度，而且会极大地降低内存的利用率。以内存池组织小对象的内存，从系统的角度看，只是一大块内存池，看不到小对象内存的分配和释放。


实现时，allocator需要维护一个存储16个空闲块列表表头的数组free_list，数组元素i是一个指向块大小为8*(i+1)字节的空闲块列表的表头，一个指向内存池起始地址的指针start_free和一个指向结束地址的指针end_free。空闲块列表节点的结构如下：

```cpp
union obj{ 
    union obj *free_list_link; 
    char client_data[1]; 
};
```

这个结构可以看做是从一个内存块中抠出4个字节大小来，当这个内存块空闲时，它存储了下个空闲块，当这个内存块交付给用户时，它存储的时用户的数据。因此，allocator中的空闲块链表可以表示成：obj* free_list[16];

allocator分配内存的算法如下：

```cpp
算法：allocate 
输入：申请内存的大小size 
输出：若分配成功，则返回一个内存的地址，否则返回NULL 
{ 
    if(size大于128）{ 启动第一级分配器直接调用malloc分配所需的内存并返回内存地址； 
    else { 
        将size向上round up成8的倍数并根据大小从free_list中取对应的表头free_list_head; 
        if(free_list_head不为空）{ 
            从该列表中取下第一个空闲块并调整free_list； 
            返回free_list_head; 
        } 
     } else { 
        调用refill算法建立空闲块列表并返回所需的内存地址； 
    } 
}
```

重新填充算法：

```cpp
算法： refill 
输入：内存块的大小size 
输出：建立空闲块链表并返回第一个可用的内存块地址 
{ 
    调用chunk_alloc算法分配若干个大小为size的连续内存区域并返回起始地址chunk和成功分配的块数nobj； 
    if(块数为1)直接返回chunk； 
    else{ 
        开始在chunk地址块中建立free_list; 
        根据size取free_list中对应的表头元素free_list_head; 
        将free_list_head指向chunk中偏移起始地址为size的地址处, 即free_list_head=(obj*)(chunk+size); 
        再将整个chunk中剩下的nobj-1个内存块串联起来构成一个空闲列表； 
        返回chunk，即chunk中第一块空闲的内存块； 
      } 
   }
}
```

块分配算法

```cpp
算法：chunk_alloc 
输入：内存块的大小size，预分配的内存块块数nobj(以引用传递) 
输出：一块连续的内存区域的地址和该区域内可以容纳的内存块的块数 
{ 
    计算总共所需的内存大小total_bytes； 
    if(内存池中足以分配，即end_free - start_free >= total_bytes) { 
        则更新start_free; 
        返回旧的start_free; 
    }  
    else if(内存池中不够分配nobj个内存块，但至少可以分配一个){ 
        计算可以分配的内存块数并修改nobj； 
        更新start_free并返回原来的start_free; 
     } 
     else { //内存池连一块内存块都分配不了 
         先将内存池的内存块链入到对应的free_list中后； 
         调用malloc操作重新分配内存池，大小为2倍的total_bytes加附加量，start_free指向返回的内存地址； 
         if(分配不成功) { 
              if(16个空闲列表中尚有空闲块） 
                   尝试将16个空闲列表中空闲块回收到内存池中再调用chunk_alloc(size, nobj)； 
              else  
                   调用第一级分配器尝试out of memory机制是否还有用； 
          } 
      } 
      更新end_free为start_free+total_bytes，heap_size为2倍的total_bytes; 
      调用chunk_alloc(size,nobj); 
}
```

分配24字节大小的内存块：

假设这样一个场景，free_list[2]已经指向了大小为24字节的空闲块链表，如图1所示，当用户向allocator申请21字节大小的内存块时，allocaotr会首先检查free_list[2]并将free_list[2]所指的内存块分配给用户，然后将表头指向下一个可用的空闲块，如图2所示。注意，当内存块在链表上是，前4个字节是用作指向下一个空闲块，当分配给用户时，它是一块普通的内存区。

![](/images/posts/C++/317.png)

![](/images/posts/C++/318.png)




