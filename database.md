以Mysql数据库为基础，介绍Database。

[TOC]



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

在InnoDB存储引擎中，所有的数据都被**逻辑地**存放在表空间(tablespace)中，表空间是存储引擎中最高的存储逻辑单元，在表空间的下面又包括段(segment)、区(extent)、页(page)：

![Tablespace-segment-extent-page-row](https://raw.githubusercontent.com/Draveness/Analyze/master/contents/Database/images/mysql/Tablespace-segment-extent-page-row.jpg)

同一个数据库实例的所有表空间都有相同的页大小；默认情况下，表空间中的页大小都为16Kb，当然也可以通过改变`innodb_page_size`选项对默认进行修改，不同页大小最终也导致区大小的不同；

![Relation Between Page Size - Extent Size](https://raw.githubusercontent.com/Draveness/Analyze/master/contents/Database/images/mysql/Relation%20Between%20Page%20Size%20-%20Extent%20Size.png)

从图中可以看出，在 InnoDB 存储引擎中，一个区(extent)的大小最小为 1MB，页(page)的数量最少为 64 个。

## 1.4 如何存储表

MySQL使用InnoDB存储表时，会将**表的定义**和**数据索引**等信息分开存储，其中前者存储在`.frm`文件中，后者存储在`.ibd`中。

InnoDB 使用页作为磁盘管理的最小单位；数据在 InnoDB 存储引擎中都是按行存储的，每个 16KB 大小的页中可以存放 2-200 行的记录。 

## 1.5 索引

索引是存储引擎能够快速定位记录工具，对于提升数据库的性能、减轻数据库服务器的负担有着非常重要的作用；**索引优化是对查询性能优化的最有效手段**。

### 索引的数据结构

InnoDB 存储引擎在绝大多数情况下使用 B+ 树建立索引，这是关系型数据库中查找最为常用和有效的索引，但是 B+ 树索引并不能找到一个给定键对应的具体值，它只能找到数据行对应的页，然后正如上一节所提到的，数据库把整个页读入到内存中，并在内存中查找具体的数据行。 

![B+Tree](https://raw.githubusercontent.com/Draveness/Analyze/master/contents/Database/images/mysql/B+Tree.jpg) 

B+ 树是平衡树，它查找任意节点所耗费的时间都是完全相同的，比较的次数就是 B+ 树的高度 

### 聚集索引和辅助索引

 	B+ 树索引可以分为聚集索引（clustered index）和辅助索引（secondary index），它们之间的最大区别就是，聚集索引中存放着一条行记录的全部信息，而辅助索引中只包含索引列和一个用于查找对应行记录的『书签』。在 InnoDB 中这个书签就是当前记录的主键。 

![Clustered-Secondary-Index](https://raw.githubusercontent.com/Draveness/Analyze/master/contents/Database/images/mysql/Clustered-Secondary-Index.jpg)  

上图展示了一个使用辅助索引查找一条表记录的过程：通过辅助索引查找到对应的主键，最后在聚集索引中使用主键获取对应的行记录，这也是通常情况下行记录的查找方式。 

# 二、锁

锁的种类一般分为乐观锁和悲观锁两种，InnoDB 存储引擎中使用的就是悲观锁，而按照锁的粒度划分，也可以分成行锁和表锁。

## 2.1 乐观锁(Optimistic Lock)

顾名思义，就是很乐观，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号等机制。乐观锁适用于多读的应用类型，这样可以提高吞吐量，像数据库如果提供类似于write_condition机制的其实都是提供的乐观锁。 

## 2.2 悲观锁(Pessimistic Lock)

就是很悲观，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会block直到它拿到锁。传统的关系型数据库里边就用到了很多这种锁机制，比如行锁，表锁等，读锁，写锁等，都是在做操作之前先上锁。 

## 2.3 乐观锁vs悲观锁 优缺点

两者各有优缺点，像乐观锁适用于写比较少的情况下，即冲突真的很少发生的时候，这样可以省去了锁的开销，加大了系统的整个吞吐量。但如果经常产生冲突，上层应用会不断的进行retry，这样反倒是降低了性能，所以这种情况下用悲观锁就比较合适。 

悲观锁：当**冲突频率**和**重试成本**较高时，推荐使用。

乐观锁 ：当需要非常高的**响应速度**并且**并发量**非常大的时，推荐使用。	

- 乐观锁和悲观锁都是并发控制的机制，需要综合考虑上面的四个方面（冲突频率、重试成本、响应速度和并发量）进行选择。 

### 乐观锁应用

1. 使用自增长的整数表示数据版本号。更新时检查版本号是否一致，比如数据库中数据版本为6，更新提交时version=6+1,使用该version值(=7)与数据库version+1(=7)作比较，如果相等，则可以更新，如果不等则有可能其他程序已更新该记录，所以返回错误。
2. 使用时间戳来实现. 
   注：对于以上两种方式,Hibernate自带实现方式：在使用乐观锁的字段前加annotation: @Version, Hibernate在更新时自动校验该字段。

### 悲观锁应用

需要使用数据库的锁机制，比如SQL SERVER 的TABLOCKX（排它表锁） 此选项被选中时，SQL Server 将在整个表上置排它锁直至该命令或事务结束。这将防止其他进程读取或修改表中的数据。

## 2.4 锁的粒度

	一般的读写锁都只是对某一个数据进行加锁，InnoDB 支持多种粒度的锁，也就是行锁和表锁；为了支持多粒度锁定，InnoDB 存储引擎引入了意向锁（Intention Lock），意向锁就是一种表级锁。 

### 读写锁:

- 排它锁（Exclusive），又称写锁。
- 共享锁（Shared），又称读锁。

### 意向锁：

- **意向共享锁**：事务想要在获得表中某些记录的共享锁，需要在表上先加意向共享锁；
- **意向排它锁**：事务想要在获得表中某些记录的排它锁，需要在表上先加意向排它锁；	

意向锁其实不会阻塞全表扫描之外的任何请求，它们的主要目的是为了表示**是否有人请求锁定表中的某一行数据**。 

> 举一个例子：如果没有意向锁，当已经有人使用行锁对表中的某一行进行修改时，如果另外一个请求要对全表进行修改，那么就需要对所有的行是否被锁定进行扫描，在这种情况下，效率是非常低的；不过，在引入意向锁之后，当有人使用行锁对表中的某一行进行修改之前，会先为表添加意向互斥锁（IX），再为行记录添加互斥锁（X），在这时如果有人尝试对全表进行修改就不需要判断表中的每一行数据是否被加锁了，只需要通过等待意向互斥锁被释放就可以了。 

# 三、事务与隔离级别

## 3.1 事务

事务(Transaction)是数据库运行中的逻辑工作单位 。事务必须满足ACID 四大特性：原子性（Atomicity）、一致性（Consistency）、隔离性（Isolation）和持久性（Durability） 。

## 3.2 ACID

### 1. 原子性（Atomicity）

事务被视为不可分割的最小单元，事务的所有操作要么全部提交成功，要么全部失败回滚。

### 2.一致性（Consistency）

数据库在事务执行前后都保持一致性状态。在一致性状态下，所有事务对一个数据的读取结果都是相同的。

### 3. 隔离性（Isolation）

一个事务所做的修改在最终提交以前，对其它事务是不可见的。

### 4. 持久性（Durability）

一旦事务提交，则其所做的修改将会永远保存到数据库中。即使系统发生崩溃，事务执行的结果也不能丢失。在系统发生崩溃时，使用备份的数据库进行数据恢复。 

----

事务的 ACID 特性概念简单，但不是很好理解，主要是因为这几个特性不是一种平级关系：

- 只有满足一致性，事务的执行结果才是正确的。
- 在无并发的情况下，事务串行执行，隔离性一定能够满足。此时只要能满足原子性，就一定能满足一致性。
- 在并发的情况下，多个事务并行执行，事务不仅要满足原子性，还需要满足隔离性，才能满足一致性。
- 事务满足持久化是为了能应对数据库崩溃的情况。

![ACID.png](https://github.com/Realee007/jobbook/blob/master/src/image/ACID.png?raw=true)



## 3.3 四种隔离级别

ISO 和 ANIS SQL 标准制定了四种事务隔离级别，而 InnoDB 遵循了 SQL:1992 标准中的四种隔离级别：

- 未提交读`READ UNCOMMITED`；

- 提交读`READ COMMITED`；
- 可重复读`REPEATABLE READ`； 
- 可串行化 `SERIALIZABLE`；

每个事务的隔离级别其实都比上一级多解决了一个问题： 

- `RAED UNCOMMITED`：使用查询语句不会加锁，可能会读到未提交的行（Dirty Read）；
- `READ COMMITED`：只对记录加记录锁，而不会在记录之间加间隙锁，所以允许新的记录插入到被锁定记录的附近，所以再多次使用查询语句时，可能得到不同的结果（Non-Repeatable Read）；
- `REPEATABLE READ`：多次读取同一范围的数据会返回第一次查询的快照，不会返回不同的数据行，但是可能发生幻读（Phantom Read）；
- `SERIALIZABLE`：InnoDB 隐式地将全部的查询语句加上共享锁，解决了幻读的问题；

MySQL 中默认的事务隔离级别就是 `REPEATABLE READ`。

| 隔离级别 | 脏读 | 不可重复读 | 幻影读 |
| -------- | ---- | ---------- | ------ |
| 未提交读 | √    | √          | √      |
| 提交读   | ×    | √          | √      |
| 可重复读 | ×    | ×          | √      |
| 可串行化 | ×    | ×          | ×      |

## 3.4 通过隔离级别解决以下问题 

接下来，我们将数据库中创建如下的表并通过个例子来展示在不同的事务隔离级别之下，会发生什么样的问题：

```sql
CREATE TABLE test(
    id INT NOT NULL,
    UNIQUE(id)
);
```

### 脏读

> 在一个事务中，读取了其他事务未提交的数据。

当事务的隔离级别为 `READ UNCOMMITED` 时，我们在 `SESSION 2` 中插入的**未提交**数据在 `SESSION 1` 中是可以访问的。 

![Read-Uncommited-Dirty-Read](https://raw.githubusercontent.com/Draveness/Analyze/master/contents/Database/images/mysql/Read-Uncommited-Dirty-Read.jpg) 



### 不可重复读

> 在一个事务中，同一行记录被访问了两次却得到了不同的结果。

当事务的隔离级别为 `READ COMMITED` 时，虽然解决了脏读的问题，但是如果在 `SESSION 1` 先查询了**一行**数据，在这之后 `SESSION 2` 中修改了同一行数据并且提交了修改，在这时，如果 `SESSION 1` 中再次使用相同的查询语句，就会发现两次查询的结果不一样。 

![Read-Commited-Non-Repeatable-Read](https://raw.githubusercontent.com/Draveness/Analyze/master/contents/Database/images/mysql/Read-Commited-Non-Repeatable-Read.jpg) 

不可重复读的原因就是，在 `READ COMMITED` 的隔离级别下，存储引擎不会在查询记录时添加行锁，锁定 `id = 3` 这条记录。 

### 幻读

> 在一个事务中，同一个范围内的记录被读取时，其他事务向这个范围添加了新的记录。

重新开启了两个会话 `SESSION 1` 和 `SESSION 2`，在 `SESSION 1` 中我们查询全表的信息，没有得到任何记录；在 `SESSION 2` 中向表中插入一条数据并提交；由于 `REPEATABLE READ` 的原因，再次查询全表的数据时，我们获得到的仍然是空集，但是在向表中插入同样的数据却出现了错误。

![Repeatable-Read-Phantom-Read](https://raw.githubusercontent.com/Draveness/Analyze/master/contents/Database/images/mysql/Repeatable-Read-Phantom-Read.jpg) 

这种现象在数据库中就被称作幻读，虽然我们使用查询语句得到了一个空的集合，但是插入数据时却得到了错误，好像之前的查询是幻觉一样。

在标准的事务隔离级别中，**幻读是由更高的隔离级别 `SERIALIZABLE` 解决的，但是它也可以通过 MySQL 提供的 Next-Key 锁解决**：

![Repeatable-with-Next-Key-Lock](https://raw.githubusercontent.com/Draveness/Analyze/master/contents/Database/images/mysql/Repeatable-with-Next-Key-Lock.jpg) 

`REPEATABLE READ` 和 `READ UNCOMMITED` 其实是矛盾的，如果保证了前者就看不到已经提交的事务，如果保证了后者，就会导致两次查询的结果不同，MySQL 为我们提供了一种折中的方式，能够在 `REPEATABLE READ` 模式下加锁访问已经提交的数据，其本身并不能解决幻读的问题，而是通过文章前面提到的 Next-Key 锁来解决。 



## 数据库的事务实现原理、操作过程、如何做到事物之间的独立性等问题 



# 四、MVCC 

	多版本并发控制（Multi-Version Concurrency Control, MVCC）是 MySQL 的 InnoDB 存储引擎**实现隔离级别**的一种具体方式，用于实现**提交读**和**可重复读**这两种隔离级别。

	未提交读隔离级别总是读取最新的数据行，无需使用 MVCC。

	可串行化隔离级别需要对所有读取的行都加锁，单纯使用 MVCC 无法实现。 

## 版本号

- 系统版本号：是一个递增的数字，每开始一个新的事务，系统版本号就会自动递增。
- 事务版本号：事务开始时的系统版本号。

## 隐藏的列

	MVCC在每行记录后面都保存着两个隐藏的列，用来存储两个版本号。

- 创建版本号：指示创建一个数据行的快照时的系统版本号。
- 删除版本号：如果该快照的删除版本号大于当前事务版本号表示该快照有效，否则表示该快照已经被删除。

## Undo日志

	MVCC 使用的快照存储在Undo日志中，该日志通过回滚指针把一个数据行(Record)的所有快照连接起来。

![MVCC-UNDO.jpg](https://github.com/Realee007/jobbook/blob/master/src/image/MVCC-UNDO.jpg?raw=true)

# 五、关系数据库设计理论

## 函数依赖

	记A->B表示A函数决定B，也就是说B函数依赖于A。

	如果{A1，A2，...，An}是关系的一个或多个属性的集合，该集合函数决定了关系的其他所有属性并且是最小的，那么该集合就称为键码。

	对于 A->B，如果能找到 A 的真子集 A'，使得 A'-> B，那么 A->B 就是部分函数依赖，否则就是完全函数依赖。

	对于 A->B，B->C，则 A->C 是一个传递函数依赖。

## 异常

以下的学生课程关系的函数依赖为 Sno, Cname -> Sname, Sdept, Mname, Grade，键码为 {Sno, Cname}。也就是说，确定学生和课程之后，就能确定其它信息。

| Sno  | Sname  | Sdept  | Mname  | Cname  | Grade |
| ---- | ------ | ------ | ------ | ------ | ----- |
| 1    | 学生-1 | 学院-1 | 院长-1 | 课程-1 | 90    |
| 2    | 学生-2 | 学院-2 | 院长-2 | 课程-2 | 80    |
| 2    | 学生-2 | 学院-2 | 院长-2 | 课程-1 | 100   |
| 3    | 学生-3 | 学院-2 | 院长-2 | 课程-2 | 95    |



不符合范式的关系，会产生很多异常，主要有以下四种异常：

1. 冗余数据：例如`学生-2`出现了两次。
2. 修改异常：修改了一个记录中的信息，但是另一个记录相同的信息却没有被修改。
3. 删除异常：删除一个信息，那么也会丢失其它信息。例如:删除`课程-1`会删除第一行和第三行，那么`学生-1`的信息就会丢失。
4. 插入异常：例如想要插入一个学生的信息，如果这个学生还没选课，那么就无法插入。

## 范式

范式理论是为了解决以上提到四种异常。

高级别范式的依赖于低级别的范式，1NF 是最低级别的范式。

### 1. 第一范式 (1NF)

属性不可分。

### 2. 第二范式 (2NF)

每个非主属性完全函数依赖于键码。

可以通过分解来满足。

**分解前** 

| Sno  | Sname  | Sdept  | Mname  | Cname  | Grade |
| ---- | ------ | ------ | ------ | ------ | ----- |
| 1    | 学生-1 | 学院-1 | 院长-1 | 课程-1 | 90    |
| 2    | 学生-2 | 学院-2 | 院长-2 | 课程-2 | 80    |
| 2    | 学生-2 | 学院-2 | 院长-2 | 课程-1 | 100   |
| 3    | 学生-3 | 学院-2 | 院长-2 | 课程-2 | 95    |

以上学生课程关系中，{Sno, Cname} 为键码，有如下函数依赖：

- Sno -> Sname, Sdept
- Sdept -> Mname
- Sno, Cname-> Grade

Grade 完全函数依赖于键码，它没有任何冗余数据，每个学生的每门课都有特定的成绩。

Sname, Sdept 和 Mname 都部分依赖于键码，当一个学生选修了多门课时，这些数据就会出现多次，造成大量冗余数据。

**分解后** 

关系-1

| Sno  | Sname  | Sdept  | Mname  |
| ---- | ------ | ------ | ------ |
| 1    | 学生-1 | 学院-1 | 院长-1 |
| 2    | 学生-2 | 学院-2 | 院长-2 |
| 3    | 学生-3 | 学院-2 | 院长-2 |

有以下函数依赖：

- Sno -> Sname, Sdept
- Sdept -> Mname

关系-2

| Sno  | Cname  | Grade |
| ---- | ------ | ----- |
| 1    | 课程-1 | 90    |
| 2    | 课程-2 | 80    |
| 2    | 课程-1 | 100   |
| 3    | 课程-2 | 95    |

有以下函数依赖：

- Sno, Cname -> Grade

### 3. 第三范式 (3NF)

非主属性不传递函数依赖于键码。

上面的 关系-1 中存在以下传递函数依赖：

- Sno -> Sdept -> Mname

可以进行以下分解：

关系-11

| Sno  | Sname  | Sdept  |
| ---- | ------ | ------ |
| 1    | 学生-1 | 学院-1 |
| 2    | 学生-2 | 学院-2 |
| 3    | 学生-3 | 学院-2 |

关系-12

| Sdept  | Mname  |
| ------ | ------ |
| 学院-1 | 院长-1 |
| 学院-2 | 院长-2 |

## ER图

Entity-Relationship，实体关系图：简记E-R图。

是指以实体、关系、属性三个基本概念概括数据的基本结构，从而描述静态数据结构的概念模式。

![E-R_1.png](https://github.com/Realee007/jobbook/blob/master/src/image/E-R_1.png?raw=true)

### 实体的三种联系

包含一对一，一对多，多对多三种。

- 一对一联系（1:1）

  例如：一个球队只能有一个经理，一个经理只在一个球队任职，则球队和经理是一对一的。

  ![E-R_1to1.png](https://github.com/Realee007/jobbook/blob/master/src/image/E-R_1to1.png?raw=true)

- 一对多联系（1:n）

  例如：一门课程由一个教师任教，一个教师可以任教多门课程，则教师和课程是一对多的。

  ![E-R_1toN.png](https://github.com/Realee007/jobbook/blob/master/src/image/E-R_1toN.png?raw=true)

- 多对多联系（n:n）

  例如：一个学生可以选多门选修课，一门选修课可以被多个学生选修，则学生和选修课是多对多的。

  ![E-R_NtoN.png](https://github.com/Realee007/jobbook/blob/master/src/image/E-R_NtoN.png?raw=true)