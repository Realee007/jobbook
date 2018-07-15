# C++

## C++的特性

1. 面向对象语言，OOP（Object Oriented Programming ）具有三大特性：封装性、继承性和多态性。

   1.  所谓封装，即将任何对象都有其特定的特性（成员变量）和行为（成员函数），被封装的特性只能通过特定的行为去访问。 
   2.  继承，通过已有的数据类型来定义新的数据类型。即基类派生出来新类。 
   3.  多态

2. 强大的函数库
3. 多功能性高度灵活的语言
4. 可用于开发系统软件，即操作系统、编译器和数据库等

## C++多态是怎么实现的？

多态(polymorphism)是面向对象编程的其中独特概念，即为不同类型的实体提供单一的接口。

> polymorphism - providing a single interface to entities of different types. virtual functions provide dynamic \(run-time\) polymorphism through an interface provided by a base class. Overloaded functions and templates provide static \(compile-time\) polymorphism. TC++PL 12.2.6, 13.6.1, D&E 2.9. ---C++ creator Bjarne Stroustrup's glossary 
>
> 多态，为不同类型的实体提供单一的接口。虚函数通过基类提供的接口提供动态（运行时）多态。重载的函数和模板提供静态（编译时）多态。---C++ 创始者Bjarne Stroustrup的词汇表 \([http://www.stroustrup.com/glossary.html](http://www.stroustrup.com/glossary.html)\):

多态：

1. 静态多态（函数重载、模板）。编译器在编译时就会决定执行哪个函数，由于在程序执行之前就解析出应该调用什么函数。这种方式称为静态绑定。
2. 动态多态（虚函数即动态调度），让基类的pointer或reference得以指向其任何一个派生类的对象。编译器无法知道哪个函数会被调用，在运行时才知道实际调用的具体，这种方式称为动态绑定。

举例：

```cpp
Type1 x;
Type2 y;
f(x);
f(y);

//1. 预处理
#define f(X) ((X)+=2)  （在实际使用中，macro通常用较长的名字）
//2. 重载
void f(int& x){x +=2;}
void f(double& x){x +=2;}
//3. 模板
template<typename T>
void f(T& x){ x +=2;}
//4. 虚函数
class Base{
    public :
           virtual Base& operator+=(int)=0;
}
class X:pulibc Base
{
public:
    X（int n）:m_n(n){}
    X& operator+=(int n){m_n+=n;return *this;}
    int m_n;
}
class Y:pulibc Base
{
    Y(int n):m_n)(n){}
    Y& operator+=(int n){m_n+=n;return *this;}
    int m_n;
}
void f(Base& x){ x+=2;}    //运行时多态
```

另外还有其它分类：

1. 通用多态
   1. 参数多态，采用参数化模板，包括函数模板和类模板 ；
   2. 包含多态，基础是虚函数，即动态多态； 
2. 特定多态
   1. 过载多态，函数重载，属于静态多态 ；
   2. 强制多态，强制类型转换 ；

[具体参考,四种不同形式的多态](https://blog.csdn.net/u010104750/article/details/49611277)

## 可继承的类的实现需要注意什么问题（构造函数、析构函数）

凡基类定义有一个(或多个)虚函数，应该要将其析构函数(destructor)声明为`virtual`.因为会根据实际对象的类型选择调用哪一个析构函数，此解析操作应该在运行时进行。不然只调用基类的析构函数，没有调用派生类的析构函数，会导致内存泄露 。

`基类的纯虚析构函数一定要予以实现` , 虚析构函数工作的方式是： 最底层的派生类的析构函数最先被调用，然后各个基类的析构函数被调用。这就是说，即使是抽象类，编译器也要产生对基类的析构进行调用，所以要保证为它提供函数体 。

## 指针和引用区别

1. 地址VS别名

   指针通过对地址的操作进而改变实参，引用是以别名的方式对实参的直接处理达到同样效果。

2. NULL初始化VS必须初始化

   指针可以是NULL,而引用肯定会指向一个对象，引用应被初始化。

   ```C++
   string& rs; // 错误，引用必须被初始化
   string s("xyzzy");
   string& rs = s; // 正确，rs指向s
   
   指针没有这样的限制。
   string *ps; // 未初始化的指针，合法但危险
   ```

3. 效率

   不存在指向空值的引用，这个事实意味着使用引用的代码效率比使用指针的要高。因为在使用引用之前不需要测试它的合法性

```C++
void printDouble(const double& rd){
　　　cout << rd; // 不需要测试rd,它肯定指向一个double值
　　} 　　　 
```

​	相反，指针则应该总是被测试，防止其为空： 

```c++
void printDouble(const double *pd）
　　{
　　　if (pd)// 检查是否为NULL
       cout << *pd;
　　}
```



4. 指针可以被重新赋值以指向另一个不同的对象，但引用则总是指向在初始化时被指定的对象，以后不能改变。

## 指针和数组的不同



## const 的用法

### 1. 用处
1. 在classes外部，修饰global或namespace作用域的常量；修饰文件、函数或区块作用域中被声明为static的对象。
2. 在classes内部，修饰static和non-static成员变量。

### 2. 作用
#### 1. 对比宏（marco）
1. 编译器可以对const常量进行类型安全检查，而宏常量不行。
2. 避免不必要的内存分配，const定义的常量在程序运行过程中只有一份拷贝，而#define定义的常量是立即数,在内存中有若干个拷贝.

#### 2. 指针使用const
1. const char* p = greeting; char* const p = greeting;
2. STL迭代器的作用就像个`T*`指针，`coanst_iterator`作为为`const T*`.

#### 3. 函数使用const
1. 形参一般使用const 引用类型，可以增加效率，减少不必要的拷贝构造
2. 类函数使用const，变成常成员函数，例如`int getVal() const;`不能调用类中任何非const成员函数。

#### 4. 类相关
1. 类成员变量使用const，只能在初始化列表中赋值
2. const修饰的类对象，其中的任何成员都不能被修改，所以该对象只能调用const成员函数。

#### 5. 将const类型转化为非const类型
​      采用`const_cast<type_id>(expression)`

## RAII：“资源获取即初始化”
​	RAII是resource acquisition is initialization的缩写，意为“资源获取即初始化”。其核心是**把资源和对象的生命周期绑定，对象创建获取资源，对象销毁释放资源**。在RAII的指导下，C++把底层的资源管理问题提升到了对象生命周期管理的更高层次。 

## 函数传值、传引用、传指针区别



## 拷贝构造函数(copy constructor )

### 什么时候调用拷贝构造函数

在C++中，下面三种对象需要调用拷贝构造函数（有时也称“复制构造函数”）：

　　1.  一个对象作为函数参数，以**值传递**的方式传入函数体；

　　2. 一个对象作为函数返回值，以**值传递**的方式从函数返回；

　　3. 一个对象用于给另外一个对象进行**初始化**（常称为**拷贝初始化**）；`MyClass B; MyClass A = B;`

### 拷贝构造函数的参数类型为什么必须是引用

​	如果拷贝构造函数中的参数不是一个引用，即形如MyClass(const MyClass myclass)，那么就相当于采用了传值的方式(pass-by-value)，而传值的方式会调用该类的拷贝构造函数，**从而造成无穷递归地调用拷贝构造函数**。因此拷贝构造函数的参数必须是一个引用。 

​	如果是指针，可以编译运行。但是此时这个函数已经不是一个拷贝构造函数了，而是一个单参数构造函数，编译器会自己生成一个默认拷贝构造函数

### 深拷贝和浅拷贝

变量A和B指向不同的存储区域，

**深拷贝**：当B被分配给存储区中的值时，A指向的值被复制到B指向的存储区域中。A和B的数据内容是分开独立的。

![deep](https://github.com/Realee007/jobbook/blob/master/src/image/deep%20copy.png?raw=true)

**浅拷贝**：当B被分配给A时，两个变量指向的是相同的区域，它们共享数据内容。

![shallow](https://github.com/Realee007/jobbook/blob/master/src/image/shallow%20copy.png?raw=true)



### 拷贝构造函数什么时候需要重写

**默认的拷贝构造函数和赋值操作符会实现浅拷贝的功能**。如果成员变量有指针类型，则当两个指针指向同一个地址，如果其中一个析构函数删除了那个内存，则另一个指针可能不会意识到它指向的内存已经消失。

**这里有个简单的规则：** 如果你需要**定义一个非空的析构函数** 或者当为**深拷贝**的时候。那么，这种情况下你也需要定义一个拷贝构造函数。

## placement new操作符 
placement new 是重载operator new 的版本,首先了解new操作符
#### new
```
class MyClass {…};
MyClass * p=new MyClass;
```
这里的new实际上是执行如下3个过程：
1. 调用operator new分配内存；
2. 调用构造函数生成类对象；
3. 返回相应指针。

#### placement new
如果你想在已经分配的内存中创建一个对象，使用new是不行的。 而placement new可以。它的原型如下
```c++
void* operator new(std::size_t, void* pMemory) throw();	//placement new
```
placement new 有一个参数：“接收一个指针指向对象该被构造之处”，即允许你在一个已经分配好的内存中（栈或堆中）构造一个新的对象。 
```c++
char *buf  = new char[sizeof(string)]; // pre-allocated buffer
string *p = new (buf) string("hi");    // placement new
string *q = new string("hi");          // ordinary heap allocation
```
​	使用new操作符分配内存需要在堆中查找足够大的剩余空间，这个操作速度是很慢的，而且有可能出现无法分配内存的异常（空间不够）。placement new就可以解决这个问题。我们构造对象都是在一个预先准备好了的内存缓冲区中进行，不需要查找内存，内存分配的时间是常数；而且不会出现在程序运行中途出现内存不足的异常。 

## 对象池

对象池用来通常用来避免重复的内存分配和销毁及内存碎片的产生.

适用的情况如下：

1. 当你需要频繁地创建和销毁对象时
2. 对象的大小类似
3. 堆内存的分配有可能造成内存碎片时
4. 每个对象有封装一些自己的资源类似数据库或网络连接，很难获取及复用

https://blog.csdn.net/l773575310/article/details/71601460(unity3d 飞机子弹模型)

## c/c++中各种变量的位置 

以Unix ELF(Executalbe and Linkable Format)格式文件为例说明编译器是怎么安排全局变量，静态变量和自动变量的位置的。 

1. 全局变量：已初始化的保存在.data段中，未初始化的表示为.bss段的一个占字符。
2. 静态变量：同全局变量.
3. 非静态局部变量：在运行时保存在栈中。既不在.data段也不再.bss段



## C++虚函数的实现 

通过在基类中声明虚函数，可以让我们定义一个基类的指针，其指向一个继承类，当通过基类的指针去调用函数时，可以在运行时决定该调用是基类函数还是继承类函数。虚函数是实现多态（动态绑定）/接口函数的基础。

虚函数具体分为2个步骤：

1. 每个class产生出一堆指向virtual functions的指针，放在一个称为virtual table（**vtbl**）表格中。
2. 每个class object被安插一个指针，指向相关的virtual table.通常这个指针被称为**vptr**.

```c++
class Base1
{
    public:
    	int base1_1;
    	int base1_1;
    virtual void base1_fun1(){}
    virtual void base1_fun1(){}
}
```

Base1的内存分布情况：

| sizeof() |      |
| -------- | ---- |
|          |      |
|          |      |
|          |      |

