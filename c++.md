#  C++

## C++的特性

1. 面向对象语言，OOP（Object Oriented Programming ）具有三大特性：封装性、继承性和多态性。

   1.  所谓封装，即将任何对象都有其特定的特性（成员变量）和行为（成员函数），被封装的特性只能通过特定的行为去访问。  使代码模块化 
   2.  继承，通过已有的数据类型来定义新的数据类型。即基类派生出来新类。 提高了代码的复用 。
   3.  多态， 实现接口重用 
2. 强大的函数库
3. 多功能性高度灵活的语言
4. 可用于开发系统软件，即操作系统、编译器和数据库等

## 继承

基类（父类，超类)、派生类（子类）

- 基类，父类，超类是指被继承的类，派生类，子类是指继承于基类的类．
- 在C++中使用：冒号表示继承，如class A:public B；表示派生类Ａ从基类Ｂ继承而来
- 派生类包含基类的所有成员，而且还包括自已特有的成员，派生类和派生类对象访问基类中的成员就像访问自已的成员一样，可以直接使用，不需加任何操作符，但**派生类无法访问基类中的私有成员．**
- 在Ｃ++中派生类可以同时从多个基类继承，Java 不充许这种多重继承，当继承多个基类时，使用逗号将基类隔开．
- 基类访问控制符，class A:public B 基类以公有方式被继承，A:private B 基类以私有方式被继承，A:protected B 基类以受保护方式被继承，如果没有访问控制符则默认为私有继承。
  1. 如果基类以public 公有方式被继承，则基类的所有公有成员都会成为派生类的公有成员．受保护的基类成员成为派生类的受保护成员.
  2. 如果基类以private 私有被继承，则基类的所有公有成员都会成为派生类的私有成员．基类的受保护成员成为派生类的私有成员．
  3. 如果基类以protected 受保护方式被继承，那么基类的所有公有和受保护成员都会变成派生类的受保护成员．

- protected 受保护的访问权限；使用protected 保护权限表明这个成员是私有的，但在派生类中可以访问基类中的受保护成员。派生类的对象就不能访问受保护的成员了。

- 不管基类以何种方式被继承，基类的私有成员，仍然保有其私有性，被派生的子类不能访问基类的私有成员．

## 多态是怎么实现的？

**多态(polymorphism)是面向对象编程的其中独特概念，即为不同类型的实体提供单一的接口。**

> polymorphism - providing a single interface to entities of different types. virtual functions provide dynamic \(run-time\) polymorphism through an interface provided by a base class. Overloaded functions and templates provide static \(compile-time\) polymorphism. TC++PL 12.2.6, 13.6.1, D&E 2.9. ---C++ creator Bjarne Stroustrup's glossary 
>
> 多态，为不同类型的实体提供单一的接口。虚函数通过基类提供的接口提供动态（运行时）多态。重载的函数和模板提供静态（编译时）多态。---C++ 创始者Bjarne Stroustrup的词汇表 \([http://www.stroustrup.com/glossary.html](http://www.stroustrup.com/glossary.html)\):

### 静态多态（函数重载、模板）

编译器在编译时就会决定执行哪个函数，由于在程序执行之前就解析出应该调用什么函数。这种方式称为静态绑定。

### 动态多态（虚函数即动态调度）

让基类的pointer或reference得以指向其任何一个派生类的对象。编译器无法知道哪个函数会被调用，在运行时才知道实际调用的具体，这种方式称为动态绑定。

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

基类的纯虚析构函数一定要予以实现 , 虚析构函数工作的方式是： 最底层的派生类的析构函数最先被调用，然后各个基类的析构函数被调用。这就是说，即使是抽象类，编译器也要产生对基类的析构进行调用，所以要保证为它提供函数体 。

## 析构函数能否为私有

- 当我们规定类只能在堆上分配内存时，就可以将析构函数声明为私有的。

	delete函数可以通过友元(friend)或者内部成员(member)函数(单例模式:静态成员函数)。

- 如果在栈上分配空间，对象在离开作用域时会调用析构函数释放空间。

## C++调用约定

C++ 语言有 `__cdecl`、`__stdcall`、`__fastcall`、`naked`、`__pascal`、`__thiscall`，比 C 语言多出一种 `__thiscall `调用方式。 

调用约定跟堆栈清除密切相关。 

这是 C++ 语言特有的一种调用方式，用于类成员函数的调用约定。如果参数确定，this 指针存放于 ECX 寄存器，函数自身清理堆栈；如果参数不确定，this指针在所有参数入栈后再入栈，调用者清理栈。`__thiscall `不是关键字，程序员不能使用。参数按照从右至左的方式入栈。 

## 函数传值、传引用、传指针区别

pass-by-value:当用这种方式传递形参时，会在函数调用的堆栈上，产生一份实参值的副本，然后将副本传递给被调用的函数，对副本的修改不影响调用者中原始变量的值。 

pass-by-reference:当用这种方式传递形参时，调用者使得被调用函数可以直接访问调用者的数据，并且如果被调用函数需要，还可以修改这些数据。 

按引用传递，分为用引用参数和用指针参数。 

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

	相反，指针则应该总是被测试，防止其为空： 

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

**指针的数组**：它是一个数组，数组内的元素都是指针。数组占多少个字节由数组本身的大小决定，每一个元素都是一个指针，在32 位系统下任何类型的指针永远是占4 个字节 。

**数组的指针**：它是一个指针，指向一个数组。在32 位系统下任何类型的指针永远是占4 个字节 。

![array_and_Pointer.jpg](https://github.com/Realee007/jobbook/blob/master/src/image/array_and_Pointer.jpg?raw=true) 



对“数组和指针是几乎相同的”误解会容易如下代码：

```c++
int *p;
p[3] = ... <---突然使用没有指向内存区域的指针
```

自动变量的指针在初期状态，值是不稳定的

### 在表达式中:

数组可以被解读成指向其初始元素的指针。

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
#### 1. 对比宏(marco)
1. 编译器处理方式

   define – 在预处理阶段进行替换 
   const – 在编译时确定其值

2. 类型检查

   define – 无类型，不进行类型安全检查，可能会产生意想不到的错误 
   const – 有数据类型，编译时会进行类型检查

3. 内存空间

   define – 不分配内存，给出的是立即数，有多少次使用就进行多少次替换，在内存中会有多个拷贝，消耗内存大 
   const – 在静态存储区中分配空间，在程序运行过程中内存中只有一个拷贝

4. 其他

   在编译时， 编译器通常不为const常量分配存储空间，而是将它们保存在符号表中，这使得它成为一个编译期间的常量，没有了存储与读内存的操作，使得它的效率也很高。 
   宏替换只作替换，不做计算，不做表达式求解。

   宏定义的作用范围仅限于当前文件。 
   默认状态下，const对象只在文件内有效，当多个文件中出现了同名的const变量时，等同于在不同文件中分别定义了独立的变量。 
   如果想在多个文件之间共享const对象，必须在变量定义之前添加extern关键字（在声明和定义时都要加）。

#### 2. 指针使用const
1. char hello[] = "Hello";

   const char* p = hello; 	//const data, non-const pointer.

   char* const p = hello;	//non-const data, const pointer.

2. STL迭代器的作用就像个`T*`指针，`const_iterator`作为为`const T*`.  

   *it = 10; //错误

#### 3. 函数使用const
1. 形参一般使用const 引用类型，可以增加效率，减少不必要的拷贝构造
2. 类函数使用const，变成常成员函数，例如`int getVal() const;`不能调用类中任何非const成员函数。

#### 4. 类相关
1. 类成员变量使用const，只能在初始化列表中赋值
2. const修饰的类对象，其中的任何成员都不能被修改，所以该对象只能调用const成员函数。

#### 5. 将const类型转化为非const类型
      采用`const_cast<type_id>(expression)`

## 变量的可见性

变量可以使用的范围是由变量的**作用域决定**。不同层次的变量的作用域，就好像大小不一的纸片。把它们堆叠起来，就会发生部分纸片被遮盖的情况。我们把这种变量作用域的遮盖情况称为**变量的可见性（Visibility）**。 

```c++
{
   int a=3,b=4;
   {
      int a=5,b=6;
      {
         char a='a',b='b';
         cout <<a <<b <<endl;
      }
      cout <<a <<b <<endl;
   }
   cout <<a <<b <<endl;
 }
```

## static变量

### static作用

1. 初始化
   1. **全局static变量**，初始化在编译的时候进行。在main函数被调用之前初始化并且只初始化一次。

   2. **局部static变量**，在函数中有效，第一次进入函数初始化并且只初始化一次，以后进入函数将沿用上次的值。

   3. **类静态成员变量**的生命周期比一个具体的类对象更长一点，在类对象存在之前就存在，需要在类外面进行定义初始化（.cpp初始化）。

      如果在.h中进行static成员变量初始化，被其它文件引用时会被报重定义错误。

2. 生命期

   两者都是从main第一次执行之前到程序结束。

3. 作用域 

   **静态全局变量则限制了作用域，只在定义该变量的源文件中有效**，在同一个源程序中的其他源文件不能使用。 而普通全局变量作用域是整个源程序。C语言的模块开发时，不同源文件下的static函数可以同名 .

4. 在类中的作用

   1. 静态数据成员，类所有对象共享。（静态数据成员不能在类中初始化，实际上类定义只是在描述对象的蓝图，在其中指定初值是不允许的。）
   2. 静态成员函数，没有this指针
   3. 即使没有实例化类的对象,static的数据成员和成员函数同样可以进行使用

### static变量初始化顺序

对于出现在同一编译单元内的全局变量来说，它们的初始化的顺序与它们的声明的顺序是一致的（销毁顺序相反）。但对于不同编译单元的全局变量，C++标准并没有明确它规定它们的初始化（销毁）顺序。因此实现上完全由编译器决定。

[参考](https://stackoverflow.com/questions/1005685/c-static-initialization-order?tdsourcetag=s_pctim_aiomsg)

能够修改初始化顺序的最佳办法：

将变量包装成函数。

```c++
//由于标准（C ++ 11及以上版本），它保证初始化一次（甚至原子化！）
static type& My_static_obj() {
    static type my_static_obj_;
    return my_static_obj_;
}
```

## extern作用

1. 当它与`"C"`一起连用。如：`extern "C" void fun(int a);` 这个高速编译器在编译fun这个函数名时安C的规则去翻译相应的函数名，而不是C++,因为C在翻译的时候会把这个`fun`名字变得面目全非，以支持C的函数重载。
2. 当`extern`直接放在变量名前和函数名前时。它可以使得你再多个模块之间共享该变量或者该函数，即你在一个模块中定义它，并在其他模块中使用`extern`，因为它没有使用头文件，所以清楚地表明它只在少数模块之间共享。

在编译的过程中编译器只需要知道数据类型和名字，以便知道如何使用它所以不会报错。一旦编译完成后，链接器会针对模块`source2`该`extern`变量去所包含的模块`source1`中生成的目标代码`.obj`中找到此变量。即**extern关键字是在链接阶段起作用.**

## inline 内联函数

C++中支持内联函数，其目的是为了提高函数的执行效率，用关键字 inline 放在**函数定义的前面**.

普通函数有一个潜在的缺点：调用函数比求解等价表达式要慢得多。

在大多数的机器上，调用函数都要做很多工作：调用前要先保存寄存器，并在返回时恢复，复制实参，程序还必须转向一个新位置执行。

```c++
inline int max(int a, int b)
{
	return a > b ? a : b;
}
//则调用： 
cout<<max(a, b)<<endl;
//在编译时展开为： 
cout<<(a > b ? a : b)<<endl;
//从而消除了把 max写成函数的额外执行开销
```

内联函数**应该在头文件中定义**，这一点不同于其他函数。 **当然也可以在源文件中定义，**但此时只有定义的那个源文件可以用它完成内联，其它源文件如果只是包含该头文件使用内联函数得到话会出现链接错误，即找不到函数的定义。

虽然inline有它的好处，但也要慎用。

《Google C++编码规范》中则规定得更加明确和详细： 

只有当函数只有 10 行甚至更少时才将其定义为内联函数.

**优点** ：当函数体比较小的时候, 内联该函数可以令目标代码更加高效. 对于存取函数以及其它函数体比较短, 性能关键的函数, 鼓励使用内联.
**缺点**： 滥用内联将导致程序变慢. 内联可能使目标代码量或增或减, 这取决于内联函数的大小. 内联非常短小的存取函数通常会减少代码大小, 但内联一个相当大的函数将戏剧性的增加代码大小. 现代处理器由于更好的利用了指令缓存, 小巧的代码往往执行更快。

## volatile

确保编译器不会帮你对`volatile`对象进行优化，让一切判断如你预期的执行。 即**volatile关键字是在编译阶段起作用.** 

## 在main函数之前/之后执行

main函数是C++程序中，“用户指定”的正式启动。其实在C++调用main做了许多事情来设置环境，如初始化静态全局变量，注意在main之前，你无法完全控制静态块初始化的顺序。

在main函数之前：

1. static class member
2. global variable

在main函数之后:

`std::atexit(func)`:

	注册func指向的函数，在正常程序终止时调用（通过`std :: exit()`或从main函数返回）。 

```c++

//1.static class member （main函数之前）
bool func()
{
	cout << "\nbefore main \n";
	return true;
}
void func2()
{
	cout << "\ninside main 2\n";
}
class BeforMain
{
public:
	static bool fun;
	static void (*funPointer)();
	static int* p;
protected:
private:
};

int* BeforMain::p = nullptr;
bool BeforMain::fun = func();

void (*BeforMain::funPointer)()= func2;

//2.global variable（main函数之前）
bool b = func();

//atexit (main函数之后）
void atexit_handler_1()
{
	cout << "\nafter main\n";
}
int main()
{
	cout << "\ninside main \n";
	BeforMain::funPointer();
	std::atexit(atexit_handler_1);
	return 0;
}
//before main
//before main
//inside main
//inside main2
//after main
```

## RAII：“资源获取即初始化”
	RAII是resource acquisition is initialization的缩写，意为“资源获取即初始化”。其核心是**把资源和对象的生命周期绑定，对象创建获取资源，对象销毁释放资源**。在RAII的指导下，C++把底层的资源管理问题提升到了对象生命周期管理的更高层次。 

## 拷贝构造函数(copy constructor )

### 什么时候调用拷贝构造函数

在C++中，下面三种对象需要调用拷贝构造函数（有时也称“复制构造函数”）：

　　1.  一个对象作为函数参数，以**值传递**的方式传入函数体；

　　2. 一个对象作为函数返回值，以**值传递**的方式从函数返回；

　　3. 一个对象用于给另外一个对象进行**初始化**（常称为**拷贝初始化**）；`MyClass B; MyClass A = B;`

### 拷贝构造函数的参数类型为什么必须是引用

如果拷贝构造函数中的参数不是一个引用，即形如MyClass(const MyClass myclass)，那么就相当于采用了传值的方式(pass-by-value)，而传值的方式会调用该类的拷贝构造函数，**从而造成无穷递归地调用拷贝构造函数**。因此拷贝构造函数的参数必须是一个引用。 

如果是指针，可以编译运行。但是此时这个函数已经不是一个拷贝构造函数了，而是一个单参数构造函数，编译器会自己生成一个默认拷贝构造函数

### 深拷贝和浅拷贝

变量A和B指向不同的存储区域，

**深拷贝**：当B被分配给存储区中的值时，A指向的值被复制到B指向的存储区域中。A和B的数据内容是分开独立的。

![deep](https://github.com/Realee007/jobbook/blob/master/src/image/deep%20copy.png?raw=true)

**浅拷贝**：当B被分配给A时，两个变量指向的是相同的区域，它们共享数据内容。

![shallow](https://github.com/Realee007/jobbook/blob/master/src/image/shallow%20copy.png?raw=true)



### 拷贝构造函数什么时候需要重写

C++默认拷贝构造函数产生的问题的讨论：

**默认的拷贝构造函数和赋值操作符会实现浅拷贝的功能**。如果成员变量有指针类型，则当两个指针指向同一个地址，如果其中一个析构函数删除了那个内存，则另一个指针可能不会意识到它指向的内存已经消失。

```c++
class Computer
{
private:
	char*  brand;
	float  price;
public:
	Computer(){}
	Computer(const char* sz, float p)
	{
		//构造函数中为brand分配内存   
		brand = new char[strlen(sz) + 1];
		strcpy(brand, sz);
	}
	~Computer()
	{
		//析构函数中释放申请的内存   
		delete[] brand;
		cout << " Clear is over ! " << endl;
	}
    void print()
	{
		cout << "brand : " << brand << endl;
		cout << "price : " << price << endl;
	}
};

int main(void)
{
	Computer obj1("Dell", 7000);

	if (true)
	{
		Computer obj2(obj1);
		obj2.print();
	}
	//cOne.brand指向的动态内存此时已经被释放   
	obj1.print();
	return 0;
}
```

**这里有个简单的规则：** 如果你需要**定义一个非空的析构函数** 或者当为**深拷贝**的时候。那么，这种情况下你也需要**重写**一个拷贝构造函数。

```c++
 //自定义拷贝构造函数   
    Computer(const  Computer&  cp)   
    {   
    	//重新为brand开辟与cp.brand同等大小的内存空间   
    	brand = new char[strlen(cp.brand) + 1];   
        strcpy(brand, cp.brand);   
        price = cp.price;           
    }   
```

最后提一点，自定义的拷贝构造函数，最好也重载operator=运算符!

## placement new操作符	 
placement new 是重载operator new 的版本,首先了解new操作符
### new做了些什么
```
class MyClass {…};
MyClass * p=new MyClass;
```
这里的new实际上是执行如下3个过程：
1. 调用operator new分配内存；（对于数组是operator new[]） 
2. 调用构造函数生成类对象；
3. 返回该对象的指针。
#### new 和 malloc区别

1. malloc 函数返回的是 `void* `类型。

   对于C++，如果你写成：p = malloc (sizeof(int)); 则程序无法通过编译，报错：“不能将 void* 赋值给 int * 类型变量”。所以必须通过 `(int *)` 来将强制转换。而对于C，没有这个要求，但为了使C程序更方便的移植到C++中来，建议养成强制转换的习惯.

2. malloc函数的实参为 sizeof(int) ，用于指明一个整型数据需要的大小.

   - 陷阱1.  `malloc(0)`

     ```c++
     //面试题(2018.8.10银联)
     char *ptr;
     if((ptr = (char *)malloc(0))==NULL)
         puts("Got a null pointer");
     else
         puts("Got a valid pointer");
     ```

     根据查阅资料[malloc(0)](https://stackoverflow.com/questions/2022335/whats-the-point-in-malloc0)，表示当malloc(0)时，malloc()返回NULL或者一个唯一的待释放的指针。相比此处代码却是判断maclloc返回是NULL或者是非NULL的合法指针，是有误的。 因此输出应该是不确定的。尽管我用vs多次编译运行输出的为"Got a valid pointer" 。所以面试的时候需要质疑。

   - 更离谱的陷阱`malloc(-1)`

     ```c++
     char *p =(char*)malloc(-1);
     ```

     参考[malloc(负数)](https://stackoverflow.com/questions/17925771/what-happens-when-we-call-malloc-with-negative-parameter) ，因为malloc()的参数为size_t类型，无符号数，当填入负数会被强制转化为它对应的size_t的对应值。经过测试当这个数大于malloc()所能分配的上限时，会返回NULL。

     ```c++
     char *p = (char*)malloc(-1); 
     =>
     size_t t= (size_t)-1;  		//t=4294967295 (env: vs2013 win32)
     char * p = (char*)malloc(t);
     ```

3. new内存分配失败时，会抛出`std::bad_alloc`异常。malloc分配内存失败时返回NULL。

4. C++允许重载new/delete操作符.

5. 内存的释放所不同,delete和free

6. 是否调用构造函数/析构函数（new和delete会调用）

   1. delete操作符 
      1. 第一步：调用对象的析构函数。
      2. 第二步：编译器调用operator delete(或operator delete[])函数释放内存空间。

### placement new是new的重载版
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
	使用new操作符分配内存需要在堆中查找足够大的剩余空间，这个操作速度是很慢的，而且有可能出现无法分配内存的异常（空间不够）。
	
	placement new就可以解决这个问题。我们构造对象都是在一个预先准备好了的内存缓冲区中进行，不需要查找内存，内存分配的时间是常数；而且不会出现在程序运行中途出现内存不足的异常。 

### malloc的底层实现

首先介绍 brk，sbrk，mmap，munmap 。4个Unix system call。参考[man](http://man7.org/linux/man-pages/man2/mmap.2.html)

```c++
int brk(void *addr);
void *sbrk(intptr_t increment);

void *mmap(void *addr, size_t length, int prot, int flags,int fd, off_t offset);
int munmap(void *addr, size_t length);
```

从函数上看，

- `brk()`参数为一个地址，假如你知道了堆的起始地址，还有堆的大小，那么就可以根据brk()修改地址参数达到调整堆的目的。

- `sbrk(1)`参数为增长量，填1的话，每次让堆往栈的方向增加 1 个字节的大小空间。

- `mmap`是将文件或者其它对象映射到内存中，它是一种内存映射文件I/O方法。

  `addr`为映射的首地址，如果为NULL则系统指定。 `length`为映射空间大小，但真正的大小为`(length/pagesize +1)`，根据页大小为单位对齐。`prot`为映射的权限，`flag`为映射方式。`fd`为文件描述符，`offset`文件偏移位置，一般设为0，表示从文件头开始映射。

	其返回值为内存映射后返回虚拟内存的首地址。

- `munmap` 是在进程地址空间中解除一个映射关系，从addr位置开始释放length个字节的内存。

在malloc底层实现：

- 对于小块内存申请，一般使用`sbrk()`和`brk()`，

- 对于大块内存申请，使用`mmap()`，（`free()`使用`munmap`）

  对于brk和mmap，默认超过128K用mmap，否则用brk。因为只有当堆顶空闲内存区大于128K，内存才真正free还给内核。

[内存中的程序剖析](https://manybutfinite.com/post/anatomy-of-a-program-in-memory/)

## 函数可重入与线程安全

函数可重入(Reentrant)：一个函数被重入，表示这个函数没有执行完成，由于外部因素或内部调用，又一次进入该函数执行。

一个函数要被重入，只有两种情况:

1. 多个线程同时执行这个函数。
2. 函数自身（可能是经过多层调用后）调用自身。

一个函数被称为可重入的，表明该函数被重入之后不会产生任何不良后果。如：

```c++
int sqr(int x)
{	return x*x; }
```

 可重入函数的特点：

- 不适用任何（局部）静态或全局的非const变量。

- 不返回任何（局部）静态或全局的非const变量的指针。

- 仅依赖任何单个资源的锁

- 不调用任何不可重入的函数。

  **可重入是并发安全的强力保障**，一个可重入的函数可以在多线程环境下放心的使用。

  参考：https://blog.csdn.net/acs713/article/details/20034511

## 回调函数

使用**回调函数（Callback Functions）** 即通过函数指针调用的函数，可以把调用者与被调用者分开，调用者不关心谁是被调用者,用于通知机制 (载入文件进度条显示 ). 

`funcA( void(*funcpointer)(int,const char*), int p_num)` ，

回调函数典型的例子就是`std::sort(It.begin(),It.end(),Cmp)`,其中Cmp为一个回调函数，其中用户可以编写相应排序准则的比较函数。 



## C++虚函数的实现 

通过在基类中声明虚函数，可以让我们定义一个基类的指针，其指向一个继承类，当通过基类的指针去调用函数时，可以在运行时决定该调用是基类函数还是继承类函数。虚函数是实现多态（动态绑定）/接口函数的基础。

虚函数具体分为2个步骤：

1. 每个class产生出一堆指向virtual functions的指针，放在一个称为virtual table（**vtbl**）虚函数表中。
2. 每个class object被安插一个指针，指向相关的virtual table（虚函数表）.通常这个指针被称为**vptr**.

**类的所有对象共享这个类的虚函数表** 

虚函数表是全局共享的元素,即全局仅有一个. 放在全局数据区，编译即确定。 

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
1. 更加肯定前面我们所描述的: __vfptr只是一个指针, 她指向一个**函数指针数组**(即: 虚函数表)
2. 增加一个虚函数, 只是简单地向该类对应的虚函数表中增加一项而已, 并不会影响到类对象的大小以及布局情况

   不妨, 我们再定义一个类的变量b2, 现在再来看看__vfptr的指向: 

   ![__vfptr_2virtual_2obj.png](https://github.com/Realee007/jobbook/blob/master/src/image/__vfptr_2virtual_2obj.png?raw=true) 

通过Watch 1窗口我们看到:
1. b1和b2是类的两个变量, 理所当然, 它们的地址是不同的(见 &b1 和 &b2)
2. 虽然b1和b2是类的两个变量, 但是: 她们的__vfptr的指向却是同一个虚函数表

由此可以总结：
**同一个类的不同实例共用同一份虚函数表**, 她们都通过一个所谓的虚函数表指针__vfptr(定义为void**类型)指向该虚函数表. 
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

纯虚析构函数需要提供函数的实现，而一般纯虚函数不能有实现，原因在于，纯虚析构函数最终需要被调用，以析构基类对象，虽然是抽象类没有实体。而如果不提供该析构函数的实现，将使得在析构过程中，析构无法完成而导致析构异常的问题。 





### 实现多态调用的虚函数不能声明为inline

inlined意味着编译期将调用端的调用动作被函数本体取代，若无法知道哪个函数该被调用时，编译器没法将该函数加以inlining。  如果虚函数通过对象被调用，倒是可以inlined 



## 虚继承与菱形继承

两个子类继承同一个父类，而又有子类又分别继承这两个子类，就如下图示 

![img](https://images0.cnblogs.com/blog/273314/201312/29132932-22832e77fcfd4d58bc4b967c00dce4ba.png) 

**问题：**baseClass在类中有两个，这可能不是我们想要的结果，增加调用的困难，同时也会浪费内存资源。 

![img](https://images0.cnblogs.com/blog/273314/201312/29133909-c9dd1b20fa9547d69188eeb35fb9d605.png) 

解决办法：虚继承

对于baseClass是公用的，也就是baseClass就实例化了一个对象!

调用B,C的虚函数的时候有两个相应的虚表指向B，C，于是就成了下面的结构了。 

![img](https://images0.cnblogs.com/blog/273314/201312/29135012-fc1f0c42c7464b4b94180b5e2c580707.png) 



## sizeof

### sizeof(空类或者结构体)

如下一个类（或结构体），求 `sizeof(Test)` 的值是多少。 

```c++
class Test{};
```

测试一下的话，会发现它的大小是**1**字节,而不是0字节。

> ```
> To ensure that the addresses of two different objects will be different. For the same reason, "new" always returns pointers to distinct objects.
> 原因有两个：1) 保证两个不同的对象实例的地址不同；2) new 运算符总是返回两个不同的对象
> --- Bjarne Stroustrup 
> ```

```c++
class Derived : public Test{};
class Derived2 : public Test{char c;};
```

这 3 个类的大小都是 **1** 个字节， 

### sizeof(非空struct)

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

## struct和union的区别

上面介绍了struct需要内存对齐，

而union的不同之处在于，它所有的元素共享同一内存单元，且分配给union的内存size 由类型最大的元素 size 来确定 。

```c++
union MyUnion
{
    int a;
    double b;
    char c;
}
```

在32位的操作系统中，sizeof(MyUnion) = 8. 由于union是共享内存，所以在赋值时，它会以最后一个赋值为最终值。

```
MyUnion test;
test.a = 1;
test.b = 1.2;
test.c = 'a';
//这样你只能看到test.c = 'a'，而其它值已经被它覆盖，失去意义。
```

什么时候用到union呢？

当元素的相关性不强时，是可以使用union，这样会节省内存size。 

## C/C++中各种变量的位置

### 程序的内存分布

1. 文本段
2. 初始化数据段
3. 未初始化数据段
4. 堆栈
5. 堆

![memoryLayoutC.jpg](https://github.com/Realee007/jobbook/blob/master/src/image/memoryLayoutC.jpg?raw=true) 

1. 全局变量：已初始化的保存在.data段中，未初始化的表示为.bss段的一个占字符。
2. 静态变量：同全局变量.
3. 非静态局部变量：在运行时保存在栈中。既不在.data段也不再.bss段

### 静态成员变量的初始化

```c++
//foo.h
class foo
{
    private:
        static int i;
};
```

静态变量的初始化应该在`.cpp`中

```c++
//foo.cpp
int foo::i = 0;
```

1. 不能初始化在`.h`：如果初始化在头文件中，那么包含头文件的每个文件都将具有静态成员的定义。因此，在链接阶段，您将获得链接器错误，因为初始化变量的代码将在多个源文件中定义。 
2. 必须在类定义之外定义静态成员，并在那里提供初始化程序。 



## 动态内存分配、静态内存分配和自动内存分配

对象由编译器自动创建销毁：

	静态内存：保存局部static对象、类static数据成语以及定义在任何函数之外的变量。
	
	栈内存：保存定义在函数内的非static对象。

对象需显示销毁:

	堆：保存动态分配的对象。

- 动态内存分配（Dynamic memory allocation）是在程序运行时才进行分配的。是由malloc或new分配的空间，使用完后需要free或者delete释放掉。也是常说的堆空间(heap)。
- 静态内存分配（Static memory allocation）表示在程序启动前变量即分配好，生命周期同程序结束。这个常指全局变量，全局静态变量,和局部静态变量。
- 自动内存分配（Automatic memory allocation）表示在进入一个函数作用域后分配，在离开该作用域后被释放。这个常指函数体内非静态的变量，也是常说的stack内存。

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

```c++
int a;
int &&r1 = c;             # 编译失败
int &&r2 = std::move(a);  # 编译通过
```
## ++运算符重载

具体实现如下：

```c++
Myclass& operator ++()				//返回引用
{
    i = i+1;
    return *this
}
Myclass operator ++(int)		 //返回变量本身
{
    Myclass tmp = *this;		//临时变量
    ++(*this);				   //调用前++
    return tmp;				   		
}
```

由于后缀++返回的是编译器自动分配的临时变量tmp，它并不是程序中定义的可寻址变量的引用。也就是不能通过地址对其返回值tmp进行操作。即**后缀++不能作为左值**。而前缀++可以作为左值，因为它返回的是本身。

## 模板（template）

### 模板的优势

1. 模板是为了重用代码(reuse source code)。特别是程序库开发人员，标准库大量采用了模板技术 。
2. TMP(Template metaprogramming) 模板元编程，可将工作从运行期转移到编译器，因而可以实现早起侦错和运行时更高效。

这里需要提4个名词：

1. 函数模板(function template)
2. 模板函数(template function)
3. 类模板(class template)
4. 模板类(template class)

**函数模板的声明和模板函数的生成**，函数模板实例化生成模板函数 ,对于模板函数常见的有sort function.

而类模板的声明和模板类的实现，对于模板类常见的有容器类如stack、list等

### 模板的缺点

1. 模板是一种编译期间生成代码的行为，将工作从运行期转移到编译器，所以编译时间会变长。

   ```c++
   template<unsigned n>			//一般情况：Factorial<n>的值是
   struct Factorial {				//n乘以Factorial<n-1>的值.
       enum {
           value = n * Factorial<n-1>::value
       };
   };
   
   template<>						//特殊情况:
   struct Factorial<0> {			 //Factorial<0>的值是1	.
       enum {
           value = 1
       };
   };
   int main() {
       std::cout << Factorial<5>::value;			//打印120
       std::cout << Factorial<10>::value;			//打印3628800
   }
   ```

   普通的程序的阶乘运算发生在运行阶段，而这个程序的阶乘运算发生在编译阶段！ 

2. 模板一旦出错想确定错误位置和错误原因，都是比较复杂的.

3. 语法不够直观，可读性略弱。

### 模板的实例化

比如一个模板:

```
template<class T>
void swap(T& a, T& b)
{
    T temp; temp =a; a=b; b=temp;
}
```

1. 显式实例化

   `template void swap<int>()` //无需给该函数重新编写函数体，这个只是**声明.**

   作用：提高效率，编译器在编译的过程中会根据显示实例化指定的类型生成模板实例。

2. 隐式实例化

   在使用模板之前，编译器不生成模板的声明和定义实例。只有当使用模板时，编译器才根据模板定义生成相应类型的实例。 

   ```c++
   int i=0,j=1;
   swap(i,j);		//编译器会根据参数i,j的类型隐式地生成swap<int>(int& a,int &b)的函数定义
   ```

### 模板的具体化

**类模板**

```c++
template <class T> 
class vector{//…//}; // (a) 普通型
template class vector<typename> ; // (b) 的显式实例化
template <class T> 
class vector<T*>{//…//}; // (c) 对指针类型特化
template <> class vector <void*>{//…//}; // (d) 对void*进行特化
```

**函数模板**

```c++
void swap(int &a, int &b){} // 普通的函数
template<> swap<int>(int &a, int &b){} // 特化的模板函数
template void swap<int>(int &a, int &b); // 显式实例化，这个只用声明就行
template<class T> void swap(T &a, T &b){} // 模板
```

参考：https://blog.csdn.net/chenyiming_1990/article/details/10526371

## C++11的基本特性

### auto关键字

在C++11之前，auto关键字是用来指定存储期(即自动存储期auto变量).

在新标准中，它的功能变为**类型推断**。通知编译器去根据初始化代码推断所声明变量的真实类型。一般不能来声明函数的返回值。

### nullptr

减少了使用0表示空指针，被隐式类型转换为整型的危险。

### 基于范围的for循环

C++11扩展了for语句的语法，不关心下标、迭代器位置可以直接遍历容器。

### override和final

override，表示当前函数应当重写基类中的虚函数。

 final，表示派生类不应当重写这个虚函数。  

特例1:

```c++
class B 
{
public:
   virtual void f(short) {std::cout << "B::f" << std::endl;}
};

class D : public B
{
public:
   virtual void f(int) {std::cout << "D::f" << std::endl;}
};
/*
B* p = new D();
int a = 2;
p->f(a);			//输出 B::f
*/
```

特例1中，当你通过基类指针调用子类对象时，实际打印的是`B::f`而不是`D::f`.这里`D::f`是**重载Overload** 而**不是重写Override** .

特例2：

```c++
class B 
{
public:
   virtual void f(int) const {std::cout << "B::f " << std::endl;}
};

class D : public B
{
public:
   virtual void f(int) {std::cout << "D::f" << std::endl;}
};
/*
B* p = new D();
p->f(2);			//输出 B::f
*/
```

特例2中,参数相同，但是基类的函数是const的，派生类的函数却不是。 同样`D::f`是重载而不是重写.

以上如果使用`override`关键字，程序会在编译的时候报错：“D1::f”: 包含重写说明符“override”的方法没有重写任何基类方法。

### 强类型枚举

在传统的C++枚举类型存在一些缺陷：

1. 它们会将枚举常量暴露在外层作用域中。

   ```
    enum class Side{ Right, Left };
    enum class Thing{ Wrong, Right };
    //编译会报错，因为Right重定义
   ```

2. 会被隐式转换为整型。

强类型枚举由关键字**enum class**标识 .修正了以上的缺陷。

### std::function

`std::function`是一个模板类，对C++中各种可调用实体（普通函数、函数指针、Lambada表达式，重载了函数调用的类等）的封装，形成一个新的可调用的std::function对象.最大的用处就是在**实现函数回调**.

`std::function<int(int,int)>`  声明了一个function类型，接收两个int、返回一个int的可调用对象。

```c++
#include <functional>
#include <iostream>
using namespace std;

std::function< int(int,int)> Functional;

// 普通函数
int add(int a, int b) { return a + b; }

// Lambda表达式
auto lambda = [](int a,int b)->int{ return a*b; };

// 函数对象类的对象(仿函数):重载括号运算符的类
class divide
{
public:
	int operator()(int a,int b)
	{
		return a/b;
	}
};

// 1.类成员函数
// 2.类静态函数
class TestClass
{
public:
	int ClassMember(int a,int b) { return a+b; }
	static int StaticMember(int a,int b) { return a+b; }
};

int main()
{
	// 普通函数
	Functional = add;
	int result = Functional(1,2);
	cout << "普通函数：" << result << endl;

	// Lambda表达式
	Functional = lambda;
	result = Functional(1,2);
	cout << "Lambda表达式：" << result << endl;

	// 仿函数
	Functional = divide();
	result = Functional(4,2);
	cout << "仿函数：" << result << endl;

	// 类成员函数
	TestClass testObj;
	//C++11的新特性：占位符std::placeholders,其中_1, _2, _3是未指定的数字对象.
	//用于function的bind中。 _1用于代替回调函数中的第一个参数， _2用于代替回调函数中的第二个参数，以此类推。
	Functional = std::bind(&TestClass::ClassMember, testObj, std::placeholders::_1, std::placeholders::_2);
	result = Functional(1,2);
	cout << "类成员函数：" << result << endl;

	// 类静态函数
	Functional = TestClass::StaticMember;
	result = Functional(1,2);
	cout << "类静态函数：" << result << endl;

	return 0;
}
```

### Lambda表达式

一个lambda表达式表示一个可调用的代码单元，我们可以将其理解为一个未命名的内联函数。它与函数类似，具有返回类型、参数列表、函数体。和函数不同的是lambda可定义在函数内部。

基本语法如下：  

`[捕获列表](参数列表)-> 返回类型{函数体}`

1. capture list（捕获列表）：lambda所在函数中定义的局部变量的列表（通常为空）.
2. return type:返回类型；
3. parameter list: 参数列表;
4. function body:函数体.

与普通函数不同的是，lambda必须使用尾置返回(trailing return type). 尾置返回类型跟在形参列表后面并以一个->符号开头。表示函数真正的返回类型跟在形参列表之后。

可以忽略参数列表和返回类型，但必须存在 捕获列表`[]`和函数体`{}`:`auto f =[] {return 4;}`。

参考 ：https://www.kancloud.cn/wangshubo1989/new-characteristics/99707

### std::thread

1. 默认构造函数：`thread() noexcept`(在C++11中noexcept说明指定某个函数不会抛出异常).
	作用：创建一个空的`std::thread`执行对象。
2. 初始化构造函数:
```c++
template <class Fn, class Args....>
explicit thread(Fn&& fn, Args&&... args);
```
	作用：创建一个`std::thread`对象，可被`joinable`,新产生的线程会调用`fn`函数，该函数由`args`给出
3. 拷贝构造函数(deleted) `thread(const thread&) = delete;`
    意味`std::thread`对象不可拷贝构造。
4. Move 构造函数 `thread(thread&& x) noexcept`
    `std::move(x)`，调用成功后x不再是一个`std::thread`对象。
    注意**可被`joinable`的`std::thread`对象必须在他们销毁之前被主线程`join`或者将其设置为`detached`，不然线程对象未被释放掉

### thread_local数据

一个thread_local变量是一个thread的专有对象，其他thread不能访问，除非其拥有者将指向它的指针提供给了其他线程。 普通的局部变量有自己作用域生命期，而thread_local变量只有thread活跃它就活跃。

- 提供thread显示缓存互斥访问数据，避免了数据竞争。

### 初始化变量的改变（防止线程竞争）

在新的C++11之后，新标准规定：

当一个线程正在初始化一个变量的时候，其他线程必须得等到该初始化完成以后才能访问它。


### initializer_list

和vector一样，initializer_list也是模板类型。

```c++
class CompareClass 
{
  CompareClass (int,int);
  CompareClass (initializer_list<int>);
};

int main（）
{
    myclass foo {10,20};  // calls initializer_list ctor
    myclass bar (10,20);  // calls first constructor 
}
```

### 智能指针

`#include<memory>`

1. `shared_ptr`允许多个指针指向同一个对象,“共享”。
2. `unique_ptr`“独占”所指向的对象。
3. `weak_ptr`弱引用，指向`shared_ptr`的弱引用.将`weak_ptr`绑定到`shared_ptr`不会改变其引用计数 

#### shared_ptr和unique_ptr区别

使用`unique_ptr`某一时刻只能有一个`unique_ptr`指向一个指定对象。当`unique_ptr`被销毁时，它所指的对象也被销毁。虽然不能拷贝或赋值`unique_ptr`但是可以通过realease或reset将指针的所有权从一个(非const)`unique_ptr`转移到另一个:

```
unique_ptr<T> myPtr(new T);       // Okay
unique_ptr<T> myOtherPtr = myPtr; // Error: Can't copy unique_ptr

unique_ptr<string> p2(p1.release());//p1.release()返回p1,并将p1置为空
unique_ptr<string> p3(new string("Text"));
p2.reset(p3.release());			//p2.reset()释放了p2原来的内存,令p2指向了p3.
```

使用`shared_ptr`允许多个指针指向同一个对象，当最后一个资源被销毁时，才会被自动释放。

```
shared_ptr<string> myPtr(new string("shared_ptr")); // Okay
shared_ptr<string> myOtherPtr1 = myPtr; // Sure!  Now have two pointers to the resource.
shared_ptr<string> myOtherPtr2 = myPtr; // Sure!  Now have three pointers to the resource.
```

在内部，`shared_ptr`使用引用计数来跟踪有多少指针引用资源 。但是**这里注意要避免-- 循环引用**

#### 智能指针的循环引用

“循环引用”简单来说就是：两个对象互相使用一个shared_ptr成员变量指向对方的会造成循环引用。导致引用计数失效。下面给段代码来说明循环引用： 

```c++
class B;
class A
{
public:
	~A(){cout<<"delete A";}
	std::shared_ptr <B> m_b;
};
class B
{
public:
	~B(){cout << "delete B" }
	std::shared_ptr <A> m_a;
};
int main()
{
	std::shared_ptr<A> a(new A); //new出来的A的引用计数此时为1
	std::shared_ptr<B> b(new B); //new出来的B的引用计数此时为1
	std::shared_ptr<int> c; //new出来的c的引用计数此时为0
	if (true)
	{
		a->m_b = b; //B的引用计数增加为2
		b->m_a = a; //A的引用计数增加为2
		std::shared_ptr<int> myptr(new int(10));
		c = myptr;					//c的引用计数此时为2
	}
	cout <<"a use count"<< a.use_count() << endl;		//a的引用计数此时为2
	cout <<"b use count" << b.use_count() << endl;		//b的引用计数此时为2
	cout << "c use count" << c.use_count() << endl;		//c的引用计数此时为1
	return 0;
}
```

 注意此时a,b都没有释放，产生了内存泄露问题！ 

即A内部有指向B，B内部有指向A，这样对于A，B必定是在A析构后B才析构，对于B，A必定是在B析构后才析构A，这就是循环引用问题，违反常规，导致内存泄露 . 

解决的较佳方法是使用**weak_ptr**打破这种循环引用。

```c++
class B;
class A
{
public:
	~A()
	{
		cout<<"delete A";
	}
	void doSomething()
	{
		shared_ptr<B> pb = m_b.lock();	//若m_b.use_count()为0，则返回一个空的shared_ptr.
										//否则返回一个纸箱m_b的对象的shared_ptr
		if (pb)
		{
			cout << "B use count" << pb.use_count() << endl;
		}
	}
	//std::shared_ptr < B > m_b;
	weak_ptr<B> m_b;
};
class B
{
public:
	~B()
	{
		cout << "delete B";
	}
//	std::shared_ptr <A> m_a;
	weak_ptr<A> m_a;

};

int main()
{
	std::shared_ptr<A> a(new A); //new出来的A的引用计数此时为1
	std::shared_ptr<B> b(new B); //new出来的B的引用计数此时为1
	std::shared_ptr<int> c; //new出来的c的引用计数此时为0
	if (true)
	{
		a->m_b = b; //B的引用计数增加为2
		b->m_a = a; //A的引用计数增加为2
		std::shared_ptr<int> myptr(new int(10));
		c = myptr;					//c的引用计数此时为2
	}
	cout <<"a use count"<< a.use_count() << endl;		//a的引用计数此时为2(返回与a共享对象的智能指针数量)
	cout <<"b use count" << b.use_count() << endl;		//b的引用计数此时为2
	cout << "c use count" << c.use_count() << endl;		//c的引用计数此时为1
	cout << "weak_ptr:" << endl;
	a->doSomething();
	cout << "B use count" << a.use_count() << endl;		//a的引用计数此时为1
	cout << "B use count" << b.use_count() << endl;		//b的引用计数此时为1
	return 0;
}
```

`weak_ptr`有两个常用的功能函数:

1. `w.expired()` 用于检测所管理的对象是否已经释放。
2. `w.lock()`用于获取所管理对象的强引用指针(`shared_ptr`)

## STL

STL (标准模版库，Standard Template Library）

六大组件

1. Container(容器) 各种基本数据结构
2. Adapter(适配器) 可改变containers、Iterators或Function object接口的一种组件
3. Algorithm(算法) 各种基本算法如sort、search…等
4. Iterator(迭代器) 连接containers和algorithms
5. Function object(函数对象) 
6. Allocator(分配器)  



## 序列式容器:其元素都可序，但未必已排好序。

### vector 

**内部数据结构：数组**。 

随机访问每个元素，所需要的时间为常量。

在末尾增加或删除元素所需时间与元素数目无关，在中间或开头增加或删除元素所需时间随元素数目呈线性变化。 

可动态增加或减少元素，内存管理自动完成，但**程序员可以使用reserve()成员函数来增加内存容量，使用C++11 shrink_to_fit()成员函数来减少容量** 。

vector扩容机制 :是按容量的**2倍进行扩容**，(空间和时间的权衡 ) .

> 为什么是容量加倍扩容策略
>
> 1. 假如是容量递增策略，即每次追加所固定的扩充： capacity += INCREMENT.
>
>    最坏情况下：在初始容量为0的空向量中，连续插入n =m*i >> 2 个元素.
>
>    于是在第1、i+1、2i+1、3i+1...插入时，都需要扩容。
>
>    即便不计申请空间，每次扩容中复制原向量的时间成本依次为
>
>    0、i、2i、.... (m-1)i			//算术级数
>
>    总耗时是 `i*(m-1)*m/2` = O(n^2)。故扩容的**时间分摊成本为O(n)**。
>
> 2. 加倍扩容策略
>
>    capacity<<=1.
>
>    最坏情况：在初始容量为1的满的向量中，连续插入n = 2^m >>2个元素.
>
>    于是，在第1、2、4、8、16...次插入时需要扩容
>
>    1、2、4、8... 2^m = n			//几何级数
>
>    总耗时 O(n)，每次扩容的**时间分摊成本为O(1)**.
>

```c++
vector <int> array;
array.push_back(1);
array.push_back(2);
array.push_back(3);
for (vector <int>::size_type i = array.size() - 1; i >= 0; --i) // 反向遍历array数组 
{
	cout << array[i] << endl;
}
size_type 是 unsigned int型的,也就是说, i=0时再执行--i则i=0xFFFFFFFF(4294967295).于是程序会崩溃.
```

### list

**内部数据结构：双向环状链表**。

不能随机访问一个元素。 可双向遍历。 在开头、末尾和中间**任何地方增加或删除**元素所需**时间都为常量**。

可动态增加或减少元素，内存管理自动完成。 增加任何元素都不会使迭代器失效。删除元素时，除了指向当前被删除元素的迭代器外，其它迭代器都不会失效。 

### slist

内部数据结构：**单向链表**。

不可双向遍历，只能从前到后地遍历。

其它的特性同list相似。

### deque 

**内部数据结构：数组（分段连续空间，通过一小块存放指针的连续空间指向另一大的连续线性空间）** 

 随机访问每个元素，所需要的时间为常量。 在开头和末尾增加元素所需时间与元素数目无关，在中间增加或删除元素所需时间随元素数目呈线性变化。 可动态增加或减少元素，内存管理自动完成，**不提供用于内存管理的成员函数。** 增加任何元素都将使deque的迭代器失效。在deque的中间删除元素将使迭代器失效。在deque的头或尾删除元素时，只有指向该元素的迭代器失效。 

### stack

**使用adapter(适配器)**，

一般以deque为底部结构（list也可以），并封闭其前端开口，即形成了stack. (修改某物接口，形成另一风貌---adapter设计模式)

所以stack被归纳为**container adapter**.

元素只能后进先出（LIFO）。

没有迭代器，不能遍历整个stack。 

### queue

**同样使用adapter**,

一般以deque为底部结构，并封闭其底端的出口，和前端的入口，即形成queue。

元素只能先进先出（FIFO）。 

没有迭代器，不能遍历整个queue。 



### heap

heap它不属于STL容器组件，但是它的特化版（binary max heap）是作为priority_queue的底层机制。

binary heap是一种完全二叉树。它的底层又是由vector和heap算法实现。

根据元素的排列方式，分为max-heap和min-heap.

max-heap:每个节点的键值都大于或等于其子节点键值。

min-heap:每个节点的键值都小于或等于其子节点键值。

**STL中使用的为max_heap**.其对应算法如下

**push_heap()**：1. 新加入的元素一定要放在最下一层最右边作为叶节点。2. 将新节点与父节点比较，如果key比父节点大，则父子对换位置，如此一致上溯，知道不需要对换或直到根节点为止。

**pop_heap()**: 1. pop根节点留下空节点，然后割舍最下一层最右边的叶节点。2. 将空节点和其较大子节点对换，并持续下溯，直至叶节点为止，然后将割舍的叶节点放入空节点。再对其执行一次上溯即可。



### priority_queue 优先级队列

以任何次序将任何元素推入容器内，但取出时一定是优先权最高（key最大）的元素开始取。

**vector作为底层存储方式。** 再加上heap处理.	可以说默认是**大根堆**。

利用pop_heap算法，pop只能访问第一个元素（key最大），不能遍历整个priority_queue。 



## 关联式容器：每个元素都有一个key和一个value。

### RB-tree底层机制

**二叉搜索树(BST)**： 任何节点的键值一定大于左子树的每一个节点的键值，并小于其右子树的每一个节点的键值(键值key可能和实值value相同也可能不同)。

插入：从根节点开始，遇键值大就向左，键值小就向右，一直到尾端。

删除：如果删除点A只有一个子节点，就直接把A的子节点连至A的父节点；如果A有两个子节点，我们就以右子树内最小节点取代A。

平衡二叉搜索树**(AVL树）**：在BST的基础上，要求任何节点的左右子树高度相差最多1。



**RB-tree(红黑树),**在BST树的基础上:

1. 每个节点非红即黑。
2. 根节点为黑
3. 父子两节点不得同时为红
4. 任意节点到达NULL节点之任一路径，所包含的黑节点数量必须相同。

插入、查找 、删除:O(logN)。即对数平均时间。

### set

内部结构：红黑树

键值key和实值value相等，并唯一。所有元素根据键值自动被排序。

set的迭代器被定义为RB-tree的const_iterator,杜绝写入操作，因为任意改变set元素值，会破坏set组织。

**面对关联式容器，应该使用其所提供的find函数来搜寻元素，比使用STL算法的find()更有效。**

### multiset

与set的唯一差别在于允许键值重复。(原因是RB-tree有两种插入的方式:1.`insert_equal()`2.`insert_unique()`)

### map 

内部结构：红黑树

元素都是pair,同时拥有key和value,并不同元素key一定不等，所有元素根据键值自动被排序。

map的迭代器介于const和muatble之间，因为元素的key不能被修改，而value可以被修改。

如果迭代器所指向的元素被删除，则该迭代器失效。其它任何增加、删除元素的操作都不会使迭代器失效。

### multimap

与map的唯一差别在于允许键值重复。

### hashtable底层机制

hashetable 插入、查找、删除：O(1)

只有当采用不合适的哈希函数才会出现最坏情况O(N)。 即常数平均时间。

hashtable是以空间效率来换时间效率的，因而内存消耗肯定要大。

哈希算法存取之所以快,是因为其直接通过关键字key得到要存取的记录内存存储位置 。

STL中，hashtable是以开链法，具体是在一个很大的Buckets vecotr（桶向量），vector的每个元素维护一个链表。STL以质数来设计vector，链表的总容量就是桶向量的大小。

插入操作：

1. 待插入的元素key，首先根据（哈希函数计算元素的位置）key值对应vector的长度取余找到在vector上的存储位置，key是否允许重复，表格重组。
2. 找到key后将对应元素加入到链表中。

删除操作：

1. 根据key根据哈希函数找到位置，并删除对应桶链表节点。



解决哈希冲突：

1. 线性探测，
2. 二次探测，
3. 开链法。

### hash_map



内部结构：hashtable.

与map相比较，它里面的元素不一定是按键值排序的，而是按照所用的hash函数分派的。 

STL中，hashtable是以开链法，具体是在一个较大的Buckets vecotr（桶向量）存对应的key，vector的每个元素维护一个链表，来存具体的value。STL的开链法是以质数来设计表格。是利用hash函数，对key进行映射到不同区域（桶）进行保存。其插入过程是：

1. 得到key
2. 通过hash函数得到hash值
3. 得到桶号(一般都为hash值对桶数求模)
4. 存放key和value在桶内。

其取值过程是:

1. 得到key
2. 通过hash函数得到hash值
3. 得到桶号(一般都为hash值对桶数求模)
4. 比较桶的内部元素是否与key相等，若都不相等，则没有找到。
5. 取出相等的记录的value。

hash_map中直接地址用hash函数生成，解决冲突，用比较函数解决。 

### 迭代器失效、解决办法 

## 调试器的原理

### 调试的过程

1. 在代码执行过程中进行中断

2. 查看变量，代码，堆栈
3. 设置断点，下一部的调试指令。

中断类型有几种：

1. 逐行
2. 逐指令
3. 断点

一般为了高效，会将断点以指令的方式设定：如int 3.

**中断过程**：

- 中断给调试器提供了一种回调。
- 中断时，由于要让被调试程序暂停接受调试，所以这个过程需要同步阻塞。 本地调试一般使用信号量，远程调试一般使用socket阻塞模式。暂停时，等待外部给调试指令。

**调试指令：**

- 调试器需要支持以下调试指令:
- step in: 执行一条指令, 如果这个指令可以跳入, 就跳入一个层次(例如函数)  
  - visual studio F11  (debugger step in)
- step out:   跳出一个层次
  - visual studio SHIFT+F11(debugger step out)
- step over:  执行一条指令, 如果指令可跳入, 在其返回后继续
  - visual studio F10(debugger step over)
- continue: 继续连续执行指令, 直到碰到断点

等等..具体内容很多

