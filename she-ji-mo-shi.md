# 设计模式

1. 设计模式帮你确定并不明显的抽象和描述这些抽象的对象。 从而寻找合适的对象。 如Strategy策略模式秒速了怎样实现可互换的算法族；State状态模式将实体的每一个状态描述为一个对象。
2. 决定一个对象应该是什么，Facade外观模式描述了怎样用对象表示完整的子系统，FlyWeight享元模式描述了如何支持大量的最小粒度的对象。Abstarct Factory抽象工厂模式和Builder生成器模式产生那些专门负责生成其他对象的对象。Visitor访问者模式和Command命令模式生成的对象专门负责实现对其他对象或者对象组的请求。
3. 设计模式通过确定接口的主要组成成分及经接口发送的数据类型，来帮助你定义接口。即对一些类的接口做了限制。如，Decorator装饰者模式和Proxy代理模式要求该模式对象的接口与被修饰的对象和受委托的对象一致。

# 创建型模式

创建型模式抽象了实例化过程。它帮助一个系统独立于如何创建、组合和表示它的一些对象。一个类创建型模式使用继承改变被实例化的类，而一个对象创建型模型将实例化托给另一个对象。 \[toc\]

### 1.工厂模式 FactoryMethod Pattern

> ... the Factory Method pattern uses inheritance and relies on a subclass to handle the desired object instantiation.  
> ...Factory Method模式使用继承，并依赖于子类来处理所需的对象实例。

假定一个对象在这里调用它自己的工厂方法。因此唯一可能改变的是返回值是一个子类。

### 2. 抽象工厂 Abstract Factory Pattern

> ... with the Abstract Factory pattern, a class delegates the responsibility of object instantiation to another object via composition ...  
> ...通过抽象工厂模式，一个类通过合成将对象实例化的责任委托给另一个对象...

这个意思是说有一个对象A想要创建一个Foo对象。它不是自己创建Foo对象（例如，使用工厂方法），而是获取不同的对象（抽象工厂）来创建Foo对象。

### 工厂模式和抽象工厂的区别 Differences between Abstract Factory and Factory Method

**Code Examples**

```text
//Factory method :
class A
{
    public:
    A(){}
    virtual ~A(){}
    void doSomething()
    {
        Foo* f = makeFoo();
        f.whatever();
    }
    virtual Foo* makeFoo()
    {
        return new RegularFoo();
    }
};

class B:public A
{
    public:
    Foo* makeFoo()
    {
        //subclass is overriding the factory method
        //to return something different
        return new SpecialFoo();
    }
}
```

```text
//Abstract Factory
class Factory
{
public:
    Factory(){}
    ~Factory(){}
    virtual Foo* makeFoo() const
    {
        return new Foo();
    }
    virtual Bar* makeBar() const
    {
        return new Bar();
    }
};

class A
{
public:
    A(Factory* p_factory) :m_factory(p_factory)
    {}
    ~A()
    {
    }
    void doSomething()
    {
        //The concrete class of "f" depends on the concrete class
        //of the factory passed into the constructor. If you provide a
        //different factory, you get a different Foo object.
        Foo* f = m_factory->makeFoo();
        f->whatever();
        delete f;
    }
private:
    Factory* m_factory;
};
```

### 3. 单例模式 Singleton

1.保证一个类只有一个实例；2.能够轻松访问一个类的唯一实例；3.能够控制实例化。

> Singleton permit lazy allocation and initialization, whereas global variables in many languages will always consume resources.
>
> 单例模式允许延迟分配和初始化，而许多语言的全局变量总是会消耗资源。

传统的单例模式如下：

```text
class Singleton 
{
public:
    static Singleton* instance()
    {
        if (s_ins==nullptr)
        {
            s_ins = new Singleton();
        }
        return s_ins;
    }

    void work()
    {
        cout << "h" << endl;
    }
protected:
private:

    Singleton(){}
    ~Singleton(){}
    Singleton(const Singleton&);                // Prevent construction by copying
    Singleton& operator=(const Singleton&);     // Prevent assignment
    static Singleton* s_ins;

};
```

//const string& 与string const&等价  
上面这种实现在单线程环境下是没有问题的，但是多线程就有问题。 分析：  
1. 例如线程A进入函数`instance`判断语句，这句话之后就挂起了，这时线程A已经认为`ins`为`NULL`，但是线程A还没创建singleton对象. 2. 又有一个线程B进入函数`instance`判断语句,因为此时A还没有创建对象，此时B同样认为`ins`变量为NULL,于是线程B继续执行，创建了一个新的singleton对象。 3. 稍后，线程A接着执行，于是也创建了一个新的singleton对象 4. 于是出现两个对象

所以传统的singleton不是线程安全的，要对待实例化的`ins`变量加上互斥锁。

#### 双重检查加锁优化模式 Double-Checked Locking Pattern

```text
    static Singleton* instance()
    {
        if(s_ins==nullptr)
        {
            Lock lock;      //不需要每次instance调用都请求加锁
                            //只需要第一次调用的时候加上锁即可
            if (s_ins==nullptr)
            {
                s_ins = new Singleton();            //step2
            }
        }
            return s_ins;
    }
```

对于 `s_ins = new Singleton();`执行这句代码，机器需要做三件事： 1. singleton对象分配空间； 2. 在分配的空间中构造对象； 3. 使`s_ins`指向分配的空间.  
遗憾的是**编译器并不是严格按照上面的顺序来执行的。可以交换2和3.**

```text
Singleton* Singleton::instance()
{
    if(s_ins==nullptr)
        Lock lock;
    if(s_ins==nullptr)
    {
        s_ins =                         //step3
        operator new(sizeof(Singleton));//step1
        new (s_ins) Singleton;          //step2
    }
    return s_ins;
}
```

好了，紧张的时刻到了，如果发生下面两件事：

* 线程A进入了instance函数，并且执行了step1和step3，然后挂起。这时的状态是：`s_ins`不NULL，而`s_ins`指向的内存去没有对象！
* 线程B进入了instance函数，发现`s_ins`不为null，就直接return `s_ins`了。

于是这里==volatile== 就登场了。

> volatile is a hint to the implementation to avoid aggressive optimization involving the object because the value of the object might be changed by means undetectable by an implementation.  
> volatile对于实现来说是一个暗示，以避免涉及对象的激进式优化，因为对象的值可能会被实现检测不到的手段所改变。

```text
static volatile Singleton* volatile instance();
```

```text
class Singleton {
public:
static volatile Singleton* volatile instance();
   ...
private:
   // one more volatile added
   static volatile Singleton* volatile pInstance;
};
// from the implementation file
volatile Singleton* volatile Singleton::pInstance = 0;
volatile Singleton* volatile Singleton::instance() {
   if (pInstance == 0) {
      Lock lock;
      if (pInstance == 0) {
        // one more volatile added
        volatile Singleton* volatile temp =
           new volatile Singleton;
        pInstance = temp;
      }
   }
   return pInstance;
}
```

1. 确保编译器不会帮你对Singleton进行优化，让一切判断如你预期的执行；
2. 确保指令在执行时不会被重新排序，造成Singleton的值有异常.

> 参考《C++ and the Perils of Double-Checked Locking》 Scott Meyers and Andrei Alexandrescu \(2004\) 也说明上述方法也不能完全解决
>
> suggest that instead of writing code like Example\(a\), clients do things like Example\(b\). 建议客户端不要像（a）那样编写代码，而要像（b）那样进行操作。通过缓存实例返回的指针来最小化此类调用。 [C++ and the Perils of Double-Checked Locking:](http://www.drdobbs.com/cpp/c-and-the-perils-of-double-checked-locki/184405772)

a

```text
Singleton::instance()->transmogrify();
Singleton::instance()->metamorphose();
Singleton::instance()->transmute();
```

b**(正确的单例模式调用方式!)**

```text
Singleton* const instance =
Singleton::instance(); // cache instance pointer
instance->transmogrify();
instance->metamorphose();
instance->transmute();
```

#### C++11单例模式

在新的C++11之后，C++新标准规定：**当一个线程正在初始化一个变量的时候，其他线程必须得等到该初始化完成以后才能访问它**。

```c++
//The beauty of the Meyers Singleton in C++11
class MySingleton
{
	public:
	static MySingleton& getInstacne()
	{
        static MySingleton instance;
        return instance;
	}
	private:
	MySingleton()=default;	//要使用该函数的编译器生成版本，因此您不需要写函数体.（C++11）
	~MySingleton()=default;
	MySingleton(const MySingleton&) = delete;
	MySingleton& operator=(const MySingleton&) = delete;//=delete意味着编译器不会为您生成这些构造函数。（C++11）
}
```

[参考单例模式，与多线程](https://www.cnblogs.com/liyuan989/p/4264889.html/)

# 结构型模式

## 适配器 Adapter 

使用适配器：

1. 想使用一个已经存在的类，而它的接口不符合你的需求。
2. 想创建一个可以复用的类，该类可以与其他不相关的类或不一定兼容的类协同工作。
3. (适用于对象Adapter)想使用一些已存在的子类，但是不可能对每一个都进行子类化以进行匹配它们的接口。对象适配器可以适配它的父类接口。

​    一个绘图编辑器，由一个Shape抽象类定义。它可以为每一种图形定义一个Shape的子类：LineShape类对应直线，PolygonShape对应于多边形等。但是对于可是显示和编辑正文的TextShape子类，并且我们现在有一个提供复杂工具类TextView类用于显示和编辑正文。

​    如果我们改变TextView类使它兼容Shape类，为实现一个应用类而破坏工具类，得不偿失。

   于是，我们可以采用：

1. 继承Shape类的**接口**和TextView的**实现**（私继承）。（Adapter模式类版本）
2. 将TextView实例作为TextShape的组成部分,与Shape的接口进行匹配来实现TextShape.（Adapter模式对象版本）

```c++
//私继承
class Adapter :
	public Target, private CAdaptee
	{};

//持有对象指针
private:
	OAdaptee* m_oa;
```

1. 私继承让Adapter能重写私基类的虚函数。而对象指针不行。
2. 私继承让Adapter能使用私基类的非私有成员。而对象指针只能被使用public成员。
3. 私继承或者说全部类型的继承`CAdaptee`都会使父类和子类耦合，即要在子类的头文件`Adapter.h`中加入`#include "CAdaptee.h"` ；而持有一个对象指针`OAdaptee*`只需加入`class OAdaptee;` 实现解耦。从而最小化文件之间的编译依赖性。 
4. 如果一个类没有非静态成员变量，没有虚函数，没有虚基类。那么私继承这种类不会占用空间( EBO, empty base optimization);而对象指针需要占用4或者8字节大小的空间 。



## Composite模式

MVC的 View视图嵌套，可以由Composite模式表述，该模式允许你创建一个类层次结构，一些子类定义了园子对象而其他类定义了组合对象，这些组合对象是由园子对象组合而成的更复杂对象。



# 行为模式

## 观察者模式

>MVC将视图与模型分离的设计可以一般化为：将对象分离，使得一个对象的改变能够影响另一些对象，而这个对象并不需要知道哪些被影响的对象的细节。这个一般设计被描述成Observer观察模式。
>
>---设计模式*GoF* 

可见观察者模式是MVC设计的一种表现形式。







## Strategy模式

MVC的View-Controller关系是Strategy模式的一个例子。一个策略是一个表述算法的对象。当你想静态或动态地替换一个算法，或有多种不同的算法，这时策略模式是非常有用的。

