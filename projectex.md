# 关键技术

## 插件化架构

https://blog.csdn.net/qq_21397217/article/details/52396346#%E5%88%86%E5%B1%82%E6%9E%B6%E6%9E%84

## 算法

### 扫描线填充算法

### DFS算法

### 贪心算法





## 开发技术

### 读取.slc文件以及.zip文件

首先对.slc文件进行校验，  
```
.slc文件数据
1. 是一种以切片（轮廓）为基本单位描述CAD实体模型2.5d文件格式,由3D systems 1994年创建。
2. 它在Z轴方向，离散的表示每层平面上轮廓点的信息。
3. 每层轮廓包含最小的Z 轴值、轮廓边界的数量以及与每一个边界相关的顶点和点的坐标。  
    1)边界信息包含顶点的数目，间隙的数目，顶点坐标。
    2)间隙的数目（number of gaps）可以用来表示顶点重复的个数。

4. 最后一层为最顶层，只需表示出Z轴值和一个无符整型（0xFFFFFFFF）。
5. 顶点列表的顺序。逆时针方向表示外部边界，顺时针方向表示内部边界。遵循右手准则，均指向模型实体内部。 
case数据如下：
Z Layer：								    0.1
Number of Boundaries：					    2
Number of Vertices for the 1st Boundary： 	5
Number of Gaps for the 1st Boundary：		0
Vertex List for 1st Boundary：				0.0, 0.0  1.0, 0.0  1.0, 1.0  0.0, 1.0  0.0, 0.0
Number of Vertices for the 2nd Boundary：	5
Number of Gaps for the 2nd Boundary：		0
Vertex List for 2nd Boundary				0.2, 0.2  0.2, 0.8  0.8, 0.8  0.8, 0.2  0.2, 0.2
...
...
...
Top of Part Maximum Z Level：               1 
Termination Value：			                0xFFFFFFFF
```
对于.slc文件会将数据以二进制的形式复写到磁盘。

载入文件进度条显示，使用**回调函数（Callback Functions）** 即通过函数指针调用的函数，`funcA( void(*funcpointer)(int,const char*), int p_num)` ，回调函数典型的例子就是`std::sort(It.begin(),It.end(),Cmp)`,其中Cmp为一个回调函数，其中用户可以自己相应排序准则的比较函数。


**类型转换**   
隐式转换为C中的遗留，显示转换是C++风格   
1. `dynamic_cast`:没有运行时类型检查来保证转换的安全性，比如在类层次结构中基类和子类之间指针或引用的转换。（上行转换：把子类的指针或者引用转换为基类表示是安全的，但下行转换：把基类指针或引用转换为子类指针或引用，由于没有动态类型的检测，所以是不安全的。）  
2. `static_cast`：在类层次间进行上行转换时，`dynamic_cast`和`static_cast`的效果是一样的；在进行下行转换时，`dynamic_cast` 具有类型检查的功能，==更安全== . (由于运行时类型检查需要运行时类型信息，而这个信息存储在类的虚函数表（关于虚函数表的概念，详细可见<Inside c++ object model>）中，==只有定义了虚函数的类才有虚函数表，没有定义虚函数的类是没有虚函数表的==。)
3. `reinterpret_cast`：非常激进的指针类型转换，在编译期完成，可以转换任何类型的指针，所以极不安全。非极端情况不要使用。   
4. `const_cast`：`const_cast`用于移除类型的`const、volatile`和`__unaligned`属性。常量指针被转化成非常量指针，并且仍然指向原来的对象；常量引用被转换成非常量引用，并且仍然指向原来的对象；常量对象被转换成非常量对象   







### Zip文件

**使用的minizip  开源项目的代码**

Minizip

https://stackoverflow.com/questions/11370908/how-do-i-use-minizip-on-zlib

### XML文件读写 && UI自动生成

XML具有自我描述性：

```
<?xml version="1.0" encoding="ISO-8859-1"?>
<note>
<to>George</to>
<from>John</from>
<heading>Reminder</heading>
<body>Don't forget the meeting!</body>
</note>
```
第一行为XML声明，它定义了XML的版本(1.0)和所使用的编码 (ISO-8859-1 = Latin-1/西欧字符集).  
下一行描述文档的**根元素**：`<note>`   
接下来4行描述根的4个子元素`(to,from,heading以及body)`   
最后一行定义根元素的结尾:`</note>`   
**XML 文档必须包含根元素**。该元素是所有其他元素的父元素。  
XML 文档中的元素形成了一棵文档树。这棵树从根部开始，并扩展到树的最底端。   
所有元素均可拥有子元素：
```
<root>
  <child>
    <subchild>.....</subchild>
  </child>
</root>
```
* XML 文档必须有根元素
* XML 文档必须有关闭标签
* XML 标签对大小写敏感
* XML 元素必须被正确的嵌套
* XML 属性必须加引号

解析XML的方式：
1. DOM(Document Object Model，文档对象模型）
    1. 【缺点】通常需要加载整个XML文档来构造层次结构，消耗资源大。
2. SAX（Simple API for XML)
    1. 【优势】不需要等待所有数据都被处理，分析就能立即开始。









### 单例程序

**采用文件锁机制实现程序单实例运行**：   
最简单自然的想法，在程序初始化的过程中检查并创建一个空的lock文件，程序结束时再删除。通过判断lock文件是否存在的方式来判断程序是否已经运行。
不过这样做有个bug，如果程序运行过程中异常终止，lock文件没有正常删除，就会导致程序无法再运行。空的lock文件不行，那么考虑在lock文件中加入一点内容，比如进程的**PID号**，然后通过检查该PID号的进程是否还在运行，就能避免上述bug了。
该方法是在脚本中经常用到限制单实例的方法，MySQL 等程序在每次启动前也会检查上次遗留的 mysql.pid 文件。  
其实，该方法是有缺陷的，因为pid会重用。

最后改进：
**给lock文件加互斥锁**，判断是否有锁来确保唯一性，如果线程crash掉而互斥量未释放则将该互斥量转移给正在等待的线程。

---
关于pid重用：  
PID是非负整数。系统和用户有进程数量的限制。进程结束后，pid可以回收，回收的pid可以重复使用,但必须延迟重用（过一段时间才能重用）。  

---

`QtSingleInstance`的例子内在`win`下使用了根据pid号为名创建`lockfile`文件，并对该`lockfile`加上`mutex`并使用`WaitForSingleObject`进行信号状态检测。  
对于可能出现的情况：
1. `WAIT_OBJECT_0`你等待的对象（互斥体）已经正常执行完成或者完成释放。
2. `WAIT_ABANDONED` 互斥体被遗弃。即占用互斥量的线程在释放互斥量之前被终止(exitThread,TerminatedThread)，那么当互斥量被遗弃的时候，系统会自动将互斥量的线程ID设为0，将它的递归计数设为0。然后系统检查有没有其他线程正在等待该互斥量。如果有，那么系统会公平的选择一个正在等待的线程，把对象内部的线程ID设为所选择的那个线程的线程ID，并将递归计数设为1，这样被选择的线程就变成可调度状态了。
3. `WAIT_TIMEOUT`：表示你等待的对象在waittime内还没完成，即超时等待。

于是我们只需要判断状态12或3 即可实现单一实例。

---
在此对于windows下处理器有两种不同的模式:
1. 用户模式User Mode。
2. 内核模式Kernel Mode。  
根据处理器上运行的代码类型，处理器在两个模式之间切换，应用程序在用户模式下运行，核心操作系统组件在内核模式下运行。多数驱动程序也在内核模式下运行，部分驱动程序在用户模式下运行。
当启动用户模式的应用程序时，进程为应用程序提供专用的“虚拟地址空间”和专用的“句柄表格”。由于应用程序的虚拟地址空间为专用空间，一个应用程序无法更改属于其他应用的数据。用户模式应用程序的虚拟地址空间除了为专用空间以外，还会受到限制。在用户模式下运行的处理器无法访问为该操作系统保留的虚拟地址，这样防止应用程序更改并可能损坏关键的操作系统数据。   
在内核模式下运行的所有代码都共享单个虚拟地址空间，这表示内核模型程序未和操作系统和其他内核模式下的程序独立开来，所有当内核模式下程序以外写入错误的虚拟地址，则对于操作系统或者其他内核模式下的程序的数据可能会受到损坏。
![image](https://msdn.microsoft.com/dynimg/IC535109.png)





### QtPlugin 插件模式

Qt的QPluginLoader类 

Qt插件本身是动态库，除此之外，它定义了一组专用的接口，从动态库中导出，供 Qt 的插件管理体系发现和调用。当你选择 Qt 插件项目模板时， Qt Creator 会自动为你插入专用接口相关的模板代码。 





## 平面点集的最小包围圆算法设计

算法思路：（平面内三点确定圆）

1. 对于平面点集P内的多个点p1,p2,......,pi(相对于i个初始边界条件)，确定出一个最小包围圆Ci.
2. 当P中引入新点pi+1时

   1. 若pi+1包含于圆Ci内，则维护原始Ci，作为点集P的最小包围圆，即Ci+1=Ci。
   2. 若pi+1不包含于圆Ci时。则需要对Ci进行更新，而且，圆Ci+1一定经过pi+1.
3. 针对2.2情况，此时最小包围圆是过pi+1且覆盖点集P={p1,p2,......,pi}的最小包围圆.则仿照之前思路：

   1. 取设置Cii的，且通过pi+1和p1,进而逐个扫描判断点集{p2,p3.......pi}。

   2. 当pk都包含于Cii，则维护Cii不变，如果存在pk不包含于Cii则，再次修改Cii，且通过pk和pi+1.再依次对点集{p1,p2,......,pi-1}进行逐个扫描。直到满足。
       算法由两层递归调用，三层循环完成。![mincircle1.png](https://github.com/Realee007/jobbook/blob/master/src/image/mincircle1.png?raw=true) 



改进算法：

1. 最远点优先渐进：由p1,p2,p3确定最小包围圆圆心后，在点集中查询距离圆心C最远的点v，若v在C内则终止。



https://people.inf.ethz.ch/gaertner/subdir/software/miniball.html



  ## MVC



## UML

### 静态视图和动态视图



### 时序图和协作图

