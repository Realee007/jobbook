# C++

## C++的特性

1. 面向对象语言，OOP（Object Oriented Programming ）具有三大特性：封装性、继承性和多态性。

   1.  所谓封装，即将任何对象都有其特定的特性（成员变量）和行为（成员函数），被封装的特性只能通过特定的行为去访问。 2.  继承，通过已有的数据类型来定义新的数据类型。即基类派生出来新类。 3. 多态

2. 强大的函数库
3. 多功能性高度灵活的语言
4. 可用于开发系统软件，即操作系统、编译器和数据库等

## C++多态是怎么实现的？

多态\(polymorphism\)是面向对象编程的其中独特概念，

> polymorphism - providing a single interface to entities of different types. virtual functions provide dynamic \(run-time\) polymorphism through an interface provided by a base class. Overloaded functions and templates provide static \(compile-time\) polymorphism. TC++PL 12.2.6, 13.6.1, D&E 2.9. ---C++ creator Bjarne Stroustrup's glossary 多态，为不同类型的实体提供单一的接口。虚函数通过基类提供的接口提供动态（运行时）多态。重载的函数和模板提供静态（编译时）多态。---C++ 创始者Bjarne Stroustrup的词汇表 \([http://www.stroustrup.com/glossary.html](http://www.stroustrup.com/glossary.html)\):

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

