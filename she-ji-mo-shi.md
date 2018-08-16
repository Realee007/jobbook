# 设计模式

1. 设计模式帮你确定并不明显的抽象和描述这些抽象的对象。 从而寻找合适的对象。 如Strategy策略模式描述了怎样实现可互换的算法族；State状态模式将实体的每一个状态描述为一个对象。
2. 决定一个对象应该是什么，Facade外观模式描述了怎样用对象表示完整的子系统，FlyWeight享元模式描述了如何支持大量的最小粒度的对象。Abstarct Factory抽象工厂模式和Builder生成器模式产生那些专门负责生成其他对象的对象。Visitor访问者模式和Command命令模式生成的对象专门负责实现对其他对象或者对象组的请求。
3. 设计模式通过确定接口的主要组成成分及经接口发送的数据类型，来帮助你定义接口。即对一些类的接口做了限制。如，Decorator装饰者模式和Proxy代理模式要求该模式对象的接口与被修饰的对象和受委托的对象一致。

# 创建型模式

**创建型模式与对象的创建有关。**

创建型模式抽象了实例化过程。它帮助一个系统独立于如何创建、组合和表示它的一些对象。一个类创建型模式使用继承改变被实例化的类，而一个对象创建型模型将实例化托给另一个对象。 \[toc\]

## 工厂模式 FactoryMethod

> ... the Factory Method pattern uses inheritance and relies on a subclass to handle the desired object instantiation.  
> ...Factory Method模式使用继承，并依赖于子类来处理所需的对象实例。

​	假定一个对象在这里调用它自己的工厂方法。因此唯一可能改变的是返回值是一个子类。

## 抽象工厂 Abstract Factory 

> ... with the Abstract Factory pattern, a class delegates the responsibility of object instantiation to another object via composition ...  
> ...通过抽象工厂模式，一个类通过合成将对象实例化的责任委托给另一个对象...

​	这个意思是说有一个对象A想要创建一个Foo对象。它不是自己创建Foo对象（例如，使用工厂方法），而是获取不同的对象（抽象工厂）来创建Foo对象。

### 工厂模式和抽象工厂的区别 

Differences between Abstract Factory and Factory Method

- 范围准则：工厂模式处理**类**间关系，而抽象工厂属于**对象**模式。

- 创建型**类模式**将对象的创建工作延迟到子类，而创建型**对象模式**将它延迟到另一个对象。

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

## 单例模式 Singleton

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

### 双重检查加锁优化模式 

Double-Checked Locking Pattern

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

### C++11 单例模式

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

**结构型模式处理类或对象的组合。**

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



## 组合模式 Composite

- MVC的 View视图嵌套，可以由Composite模式表述。

- 该模式允许你创建一个类层次结构，一些子类定义了原子对象而其他类定义了组合对象，这些组合对象是由原子对象组合而成的更复杂对象。

- 组合模式描述了如何使用递归组合，是的用户不必对这些类进行区别。


典型的composite结构如下

![composite-1.jpg](https://github.com/Realee007/jobbook/blob/master/src/image/composite-1.jpg?raw=true) 

![composite-2.jpg](https://github.com/Realee007/jobbook/blob/master/src/image/composite-2.jpg?raw=true) 



## 装饰模式 Decorator

- 装饰模式是以透明的方式给组件（对象）附加更多的功能。装饰模式可以在不需要创造更多子类的情况下，将对象的功能加以扩展。这就是装饰模式的模式动机 。
- 是继承关系的一个替代方案，提供比继承更多的灵活性。 



### 装饰模式VS适配器模式

- 装饰模式是一个接口两个类，适配器模式是两个接口一个类

- **装饰模式**：对客户端透明的方式扩展对象的功能，是继承关系的一个替代方案，提供比继承更多的灵活性。使用原来被装饰的类的一个子类的实例，把客户端的调用委派到被装饰类。

  **适配器模式**：把一个类的接口变换成客户端所期待的另一种接口，从而使原本因接口原因不匹配而无法一起工作的两个类能够一起工作。适配类可以根据参数返还一个合适的实例给客户端。 

- **装饰模式**一般在下列情况使用： 需要扩展一个类的功能或者给你个类增加附加责任；需要动态的给一个对象增加功能，这些功能可以再动态的撤销；需要增加有一些基本功能的排列组合而产生非常大量的功能，从而使得继承关系变得不现实 

- **适配器模式**一般使用的情况包括：系统需要使用现有的类，但此类已经不符合系统的需要； 

# 行为模式

**行为模式对类或对象怎样交互和怎样分配职责进行描述。**

## 观察者模式 Observer

>MVC将视图与模型分离的设计可以一般化为：将对象分离，使得一个对象的改变能够影响另一些对象，而这个对象并不需要知道哪些被影响的对象的细节。这个一般设计被描述成Observer观察模式。
>
>---设计模式*GoF* 

可见观察者模式是MVC设计的一种表现形式。

观察者模式关键对象是**目标**(subject)和**观察者**(observer)。一个或多个的观察者依赖于目标，一旦目标的状态发生改变，所有的观察者都得到通知。作为对这个通知的响应，每个观察者都将查询目标以使得其状态与目标状态同步。

MVC的Model类担任目标，而View是观察者的基类。

需要注意：

1. 目标和观察者之间的耦合。

   ​	减少耦合最简单方式的是加一个中间层。可以利用Mediator中介模式。

2. 目标和观察者之间的映射。

   ​	可显示地在目标中保存对观察者的引用，但是可能目标很多，观察者较少，这样存储代价太高。其中解决的办法是可以使用hashtable来维护目标到观察者的映射(虽然这增加了访问观察者的开销)。

3. 依赖准则的定义和维护。

   ​	不然目标上看似无害的操作，可能会引起依赖于该目标的观察者和依赖于观察者的其他对象错误的更新。解决办法可以扩展Attach注册接口和Update更新接口，让观察者注册仅对特定的事件。

   ```c++
   //使用目标对象的方面(aspects)的概念.
   void Subject::Attach(Observer*， Aspects& interest);
   void Observer::Update(Subject*, Aspects& interest);
   ```

4. 谁触发更新

     1. 由目标对象的状态设定操作，在改变目标对象的状态后，自动调用Notify。缺点是多个连续的操作可能多次连续更新，效率低。
     2. 让客户负责在适当的时候调用Notify.

5. 发出通知前确保目标的状态一致性。

   ​	比如删除某个目标后，应该让它通知观察者对该目标引用复位。

   ​	Notify作为方法中的最后一个操作。

6. 极端模型：

   推模型(push model):目标向观察者发送改变的详细信息，不管他们需要与否。目标不知道它的观察者。

   拉模型(pull model):目标知道一些观察者的需要的信息。

7. 通知默认是顺序执行的,一个观察者卡顿,会影响整体的执行效率,这种情况下,一般采用异步的方式 。

## 策略模式 Strategy

MVC的View-Controller关系是Strategy模式的一个例子。一个策略是一个表述算法的对象。当你想静态或动态地替换一个算法，或有多种不同的算法，这时策略模式是非常有用的。

**"从路径算法的类中抽出一个Strategy模式"**

适用：

1. 使用一个算法的不同变体。
2. 减少条件语句，将条件分支移入各自的Strategy类

![Strategy-uml1.jpg](https://github.com/Realee007/jobbook/blob/master/src/image/Strategy-uml1.jpg?raw=true) 



## 中介者 Mediator

当目标和观察者之前特别复杂时，可能需要一个维护这些关系的对象，我们称这样的对象为**更改管理器(ChangeManager)** 。目的是尽量减少观察者反映其目标的状态变化所需的工作量。例如，如果一个操作设计到对几个相互依赖的目标进行改动，就必须保证仅在所以的目标都已更改完毕后，才一次性地通知它们的观察者，而不是每个目标都通知观察者。

ChangeManager是一个中介者模式的实例。通常只有一个ChangeManager，并且是全局可见的。这里可使用Singleton.



# 参考

《设计模式》GOF

[《图说设计模式》](http://design-patterns.readthedocs.io/zh_CN/latest/)

