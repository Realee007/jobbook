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

### 在内存布局上：

指针则表示指向某处，数组是一些对象排列后形成的(如果是“指针的数组”和“数组的数组”就有很大的区别)

对“数组和指针是几乎相同的”误解会容易如下代码：

```c++
int *p;
p[3] = ... <---突然使用没有指向内存区域的指针
```

自动变量的指针在初期状态，值是不稳定的



### 在表达式中:

数组可以被解读成指向其初始元素的指针。

即如下

```
int* p;
int array[10];
p = array;      <---将指向array[0]的指针赋予p

但是反过来
array = p;是不可以的。因为此时的指针是一个右值，并且数组也不是“可变更的左值”
```

表达式代表某处的内存区域的时候，我们称当前的表达式为左值（lvalue） 注意这里的l不是left，而表示locator(表示位置的事物)
相对的，表达式只是代表值的时候，我们称当前的表达式为右值
但是对于下面的指针， `int* p;` 如果p指向了某个数组，自然可以通弄个p[i]的方式进行访问，但这并不代表p就是数组。
`p[i]`只不过是`*(p+i)`的语法糖。

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
### new
```
class MyClass {…};
MyClass * p=new MyClass;
```
这里的new实际上是执行如下3个过程：
1. 调用operator new分配内存；
2. 调用构造函数生成类对象；
3. 返回相应指针。
#### new 和 malloc区别

1. malloc 函数返回的是 void * 类型。对于C++，如果你写成：p = malloc (sizeof(int)); 则程序无法通过编译，报错：“不能将 void* 赋值给 int * 类型变量”。所以必须通过 (int *) 来将强制转换。而对于C，没有这个要求，但为了使C程序更方便的移植到C++中来，建议养成强制转换的习惯.
2. malloc函数的实参为 sizeof(int) ，用于指明一个整型数据需要的大小.
3. new内存分配失败时，会抛出bac_alloc异常。malloc分配内存失败时返回NULL.
4. C++允许重载new/delete操作符.
5. 内存的释放所不同,delete和free

### placement new
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

### 拥有多个虚函数的类对象

```c++
class Base1
{
    public:
    	int base1_1;
    	int base1_1;
    virtual void base1_fun1(){}
    virtual void base1_fun2(){}
}
```
Base1的内存分布情况：
| sizeof(Base1)     | 12   |
| ----------------- | ---- |
| offsetof(__vfptr) | 0    |
| offsetof(base1_1) | 4    |
| offsetof(base1_2) | 8    |

(Debug模式下, 未初始化的变量值为`0xCCCCCCCC`, 即:`-858983460`) 

![__vfptr_2virtual.png](https://github.com/Realee007/jobbook/blob/master/src/image/__vfptr_2virtual.png?raw=true) 

base1_1前面多了一个变量__vfptr(虚函数表vtable指针)，其类型为`void**`,即一个指向`void*` 数组.
再看[0]元素，类型为`void*`其值为 ConsoleApplication2.exe!Base1::base1_fun1(void) ，这其实指的是Base1::base1_fun1() 函数的地址。
可得, __vfptr的定义伪代码大概如下: 

```c++;
void* __fun[2]={&Base1::base1_fun1,&Base1::base1_fun2}
const void** __vfptr = &__fun[0];
```

通过上面图表, 我们可以得到如下结论:
1. 更加肯定前面我们所描述的: __vfptr只是一个指针, 她指向一个函数指针数组(即: 虚函数表)
2. 增加一个虚函数, 只是简单地向该类对应的虚函数表中增加一项而已, 并不会影响到类对象的大小以及布局情况

   不妨, 我们再定义一个类的变量b2, 现在再来看看__vfptr的指向: 

   ![__vfptr_2virtual_2obj.png](https://github.com/Realee007/jobbook/blob/master/src/image/__vfptr_2virtual_2obj.png?raw=true) 

通过Watch 1窗口我们看到:
1. b1和b2是类的两个变量, 理所当然, 它们的地址是不同的(见 &b1 和 &b2)
2. 虽然b1和b2是类的两个变量, 但是: 她们的__vfptr的指向却是同一个虚函数表

由此可以总结：
同一个类的不同实例共用同一份虚函数表, 她们都通过一个所谓的虚函数表指针__vfptr(定义为void**类型)指向该虚函数表. 
于是类对象的内存布局情况如下：

![class-virtual-1.png](https://github.com/Realee007/jobbook/blob/master/src/image/class-virtual-1.png?raw=true) 

虚函数表和类对象的关系：
1. 它是编译器在**编译时期**为我们创建好的, 只存在一份。
2. 定义类对象时, 编译器自动将类对象的__vfptr指向这个虚函数表。

### 子类覆盖基类虚函数

   ```c++
   class Base1
   {
   public:
       int base1_1;
       int base1_2;
   
       virtual void base1_fun1() {}
       virtual void base1_fun2() {}
   };
   
   class Derive1 : public Base1
   {
   public:
       int derive1_1;
       int derive1_2;
   
       // 覆盖基类函数
       virtual void base1_fun1() {}
   };
   ```

   ![subclassoverridevirtual.png](https://github.com/Realee007/jobbook/blob/master/src/image/subclassoverridevirtual.png?raw=true) 

   原本是Base1::base1_fun1(), 但由于**继承类重写**了基类Base1的此方法, 所以现在变成了Derive1::base1_fun1()!

   那么, 无论是通过Derive1的指针还是Base1的指针来调用此方法, 调用的都将是被继承类重写后的那个方法(函数), 多态发生鸟!!!

   那么新的布局图: 

   ![subclassoverride.png](https://github.com/Realee007/jobbook/blob/master/src/image/subclassoverride.png?raw=true) 

   这部分详细参考了：[C++中的虚函数(表)实现机制以及用C语言对其进行的模拟实现](https://blog.twofei.com/496/)

### 纯虚析构函数是一定要函数体的

​	纯虚析构函数需要提供函数的实现，而一般纯虚函数不能有实现，原因在于，纯虚析构函数最终需要被调用，以析构基类对象，虽然是抽象类没有实体。而如果不提供该析构函数的实现，将使得在析构过程中，析构无法完成而导致析构异常的问题。 

## sizeof

### sizeof（空类或者结构体）

如下一个类（或结构体），求 `sizeof(Test)` 的值是多少。 

```c++
class Test
{  
};
```

测试一下的话，会发现它的大小是**1**字节,而不是0字节。

> ```
> To ensure that the addresses of two different objects will be different. For the same reason, "new" always returns pointers to distinct objects.
> 原因有两个：1) 保证两个不同的对象实例的地址不同；2) new 运算符总是返回两个不同的对象--- Bjarne Stroustrup 
> ```

```c++
class Derived : public Test
{
};

class Derived2 : public Test
{
    char c;
};
```

这 3 个类的大小都是 **1** 个字节， 

### sizeof(非空struct)

如下：

```c++
struct MyStruct
{
  double dda1;
  char dda;
  int type;
};
```

对于32位操作系统：

1. `sizeof(double)`为8个字节，double放在首位，其起始地址跟结构体的起始地址一致为0，并且0为double(8)的倍数(0为任何数的倍数)。
2. `sizeof(char)`为1个字节，其分配的起始地址相对于结构体的起始地址为8，并且8为char（1）的倍数；
3. `sizeof(int)`为4个字节，其分配的起始地址相对于结构体为9，然而9不是int(4)的倍数，**为了满足对齐方式对偏移量的约束**，这里会自动填充3个字节。则该起始地址相对于结构体为9+3=12；
4. 结构体结束的起始地址为16.且16满足**结构体字节边界数**的要求（即是结构体中占用最大空间类型所占字节数的倍数此时为8(double类型)）。所以**sizeof(Mystruct) = 16**.

```c++
struct MyStruct2
{
  char dda;
  double dda1;
  int type;
}；
```

此sizeof(MyStruct2) = 24 

原因:1+7(对齐)+8+4+4(满足结构体的字节边界数)=24 

## 动态内存分配、静态内存分配和自动内存分配

动态内存分配（Dynamic memory allocation）是在程序运行时才进行分配的。是由malloc或new分配的空间，使用完后需要free或者delete释放掉。也是常说的堆空间(heap)。

静态内存分配（Static memory allocation）表示在程序启动前变量即分配好，生命周期同程序结束。这个常指全局变量，全局静态变量,和局部静态变量。

自动内存分配（Automatic memory allocation）表示在进入一个函数作用域后分配，在离开该作用域后被释放。这个常指函数体内非静态的变量，也是常说的stack内存。

## 左值和右值的区别

在C语言中：

1. 对于变量名，它有作为“自身的值”使用和“自身的内存区域”使用两种情况。

2. 对于表达式，也可以代表某处的内存区域。

   ```
   phoge = &hoge;
   *phoge = 10;	//*phoge是指向hoge的内存区域
   ```

   像这样，**代表某处内存区域**的时候，我们称当前的表达式为**左值(lvalue)**.

   相对的的是，表达式**只代表值**的时候，我们称当前的表达式为**右值**。

   

在形式上区分： 语法上是否能用取地址运算符。

- 赋值运算符需要一个（非常量）左值作为其左侧运算对象，得到的结果也是一个左值
- 取地址作用于一个左值运算对象，返回一个指向该运算对象的指针， **该指针是一个右值**

C++左值可以理解为，有名称的对象。

### C++11左值引用和右值引用

左值引用就是对一个左值进行引用的类型。右值引用就是对一个右值进行引用的类型，事实上，由于右值通常不具有名字，我们也只能通过引用的方式找到它的存在。 

左值引用通常也不能绑定到右值，但常量左值引用是个“万能”的引用类型。 

```c++
int &a = 2;       # 左值引用绑定到右值，编译失败

int b = 2;        # 非常量左值
const int &c = b; # 常量左值引用绑定到非常量左值，编译通过
const int d = 2;  # 常量左值
const int &e = c; # 常量左值引用绑定到常量左值，编译通过
const int &b =2;  # 常量左值引用绑定到右值，编程通过
```

右值值引用通常不能绑定到任何的左值，要想绑定一个左值到右值引用，通常需要std::move()将左值强制转换为右值，例如：

```
int a;
int &&r1 = c;             # 编译失败
int &&r2 = std::move(a);  # 编译通过
```
## 模板

模板（template）是为了可以去重用代码(reuse source code)。

这里需要提4个名词：

1. 函数模板(function template)
2. 模板函数(template function)
3. 类模板(class template)
4. 模板类(template class)

**函数模板的声明和模板函数的生成**