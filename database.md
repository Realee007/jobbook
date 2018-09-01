以Mysql数据库为基础，介绍Database。

# 一、数据库的定义

数据库并不就是一堆数据的集合，实际比这复杂的多。

- 数据库：物理操作文件系统或其他形式文件类型的集合；
- 实例：MySQL数据库由后台线程以及一个共享内存区组成；



## 1.1 数据库和实例

	在MySQL中，实例和数据库往往都是一一对应，而我们一般是无法直接操作数据库，而是通过数据库实例来操作数据库文件，可以理解为**数据库实例是数据库为上层提供的一个专门用于操作的接口**。



	在UNIX上，启动一个MySQL实例往往会产生两个进程，`mysqld`就是真正的数据库服务守护进程，而`mysqld_safe` 是一个用于检查和设置`mysqld`启动的控制程序，它负责监控MySQL进程的执行，当`mysqld`发生错误时，`mysqld_safe`会对其状态进行检查并在合适的条件下重启。

## 1.2 MySQL的架构

- 最上层用于连接、线程处理的部分并不是MySQL发明的，很多服务都有这样类似的组成部分；		
- 第二层中包含了大多数MySQL核心服务，包括对SQL的解析、分析、优化和缓存等，存储过程、触发器和视图都是在这实现。
- 第三层就是MySQL中真正负责数据的存储和提取的存储引擎。 MySQL5.5之前为 MyISAM，MYSQL5.5之后为InnoDB。

![Logical-View-of-MySQL-Architecture](https://raw.githubusercontent.com/Draveness/Analyze/master/contents/Database/images/mysql/Logical-View-of-MySQL-Architecture.jpg)

## 1.3 数据的存储

​	在InnoDB存储引擎中，所有的数据都被**逻辑地**存放在表空间(tablespace)中，表空间是存储引擎中最高的存储逻辑单元，在表空间的下面又包括段(segment)、区(extent)、页(page)：

![Tablespace-segment-extent-page-row](https://raw.githubusercontent.com/Draveness/Analyze/master/contents/Database/images/mysql/Tablespace-segment-extent-page-row.jpg)

​	同一个数据库实例的所有表空间都有相同的页大小；默认情况下，表空间中的页大小都为16Kb，当然也可以通过改变`innodb_page_size`选项对默认进行修改，不同页大小最终也导致区大小的不同；

![Relation Between Page Size - Extent Size](https://raw.githubusercontent.com/Draveness/Analyze/master/contents/Database/images/mysql/Relation%20Between%20Page%20Size%20-%20Extent%20Size.png)

​	从图中可以看出，在 InnoDB 存储引擎中，一个区(extent)的大小最小为 1MB，页(page)的数量最少为 64 个。

## 数据库的事务实现原理、操作过程、如何做到事物之间的独立性等问题 



## 数据库的脏读，幻读，不可重复读出现的原因原理，解决办法 



## 数据库的隔离级别、MVCC 





## 乐观锁，悲观锁策略

### **乐观锁**(Optimistic Lock)

顾名思义，就是很乐观，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号等机制。乐观锁适用于多读的应用类型，这样可以提高吞吐量，像数据库如果提供类似于write_condition机制的其实都是提供的乐观锁。 

### **悲观锁**(Pessimistic Lock)

就是很悲观，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会block直到它拿到锁。传统的关系型数据库里边就用到了很多这种锁机制，比如行锁，表锁等，读锁，写锁等，都是在做操作之前先上锁。 

### **乐观锁vs悲观锁 优缺点**

不可认为一种好于另一种，像乐观锁适用于写比较少的情况下，即冲突真的很少发生的时候，这样可以省去了锁的开销，加大了系统的整个吞吐量。但如果经常产生冲突，上层应用会不断的进行retry，这样反倒是降低了性能，所以这种情况下用悲观锁就比较合适。 

### 乐观锁应用

1. 使用自增长的整数表示数据版本号。更新时检查版本号是否一致，比如数据库中数据版本为6，更新提交时version=6+1,使用该version值(=7)与数据库version+1(=7)作比较，如果相等，则可以更新，如果不等则有可能其他程序已更新该记录，所以返回错误。
2. 使用时间戳来实现. 
   注：对于以上两种方式,Hibernate自带实现方式：在使用乐观锁的字段前加annotation: @Version, Hibernate在更新时自动校验该字段。

### 悲观锁应用

需要使用数据库的锁机制，比如SQL SERVER 的TABLOCKX（排它表锁） 此选项被选中时，SQL Server 将在整个表上置排它锁直至该命令或事务结束。这将防止其他进程读取或修改表中的数据。