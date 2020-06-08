---
title: 不会C++(一)(explicit,new与malloc,union)
date: 2017-05-16 15:22:22
tags: [C++]
categories: [C++]
---

这里整理一下C++语法中经常会出现的问题。。。

<!--more-->

update：2017-05-16 15:22:22

### 1.explicit关键字

> 作用是表明该构造函数是显示的，而非隐式的。

[转载的链接](http://www.cnblogs.com/ymy124/p/3632634.html)

```C++
class CxString  // 没有使用explicit关键字的类声明, 即默认为隐式声明  
{  
public:  
    char *_pstr;  
    int _size;  
    CxString(int size)  
    {  
        _size = size;                // string的预设大小  
        _pstr = malloc(size + 1);    // 分配string的内存  
        memset(_pstr, 0, size + 1);  
    }  
    CxString(const char *p)  
    {  
        int size = strlen(p);  
        _pstr = malloc(size + 1);    // 分配string的内存  
        strcpy(_pstr, p);            // 复制字符串  
        _size = strlen(_pstr);  
    }  
    // 析构函数这里不讨论, 省略...  
};  
  
    // 下面是调用:  
  
    CxString string1(24);     // 这样是OK的, 为CxString预分配24字节的大小的内存  
    CxString string2 = 10;    // 这样是OK的, 为CxString预分配10字节的大小的内存  
    CxString string3;         // 这样是不行的, 因为没有默认构造函数, 错误为: “CxString”: 没有合适的默认构造函数可用  
    CxString string4("aaaa"); // 这样是OK的  
    CxString string5 = "bbb"; // 这样也是OK的, 调用的是CxString(const char *p)  
    CxString string6 = 'c';   // 这样也是OK的, 其实调用的是CxString(int size), 且size等于'c'的ascii码  
    string1 = 2;              // 这样也是OK的, 为CxString预分配2字节的大小的内存  
    string2 = 3;              // 这样也是OK的, 为CxString预分配3字节的大小的内存  
    string3 = string1;        // 这样也是OK的, 至少编译是没问题的, 但是如果析构函数里用free释放_pstr内存指针的时候可能会报错, 完整的代码必须重载运算符"=", 并在其中处理内存释放  
```

上面的代码中, "CxString string2 = 10;" 这句为什么是可以的呢? 在C++中, 如果的构造函数只有一个参数时, 那么在编译的时候就会有一个缺省的转换操作:将该构造函数对应数据类型的数据转换为该类对象. 也就是说 "CxString string2 = 10;" 这段代码, 编译器自动将整型转换为CxString类对象, 实际上等同于下面的操作:

```C++
CxString string2(10);  
或  
CxString temp(10);  
CxString string2 = temp;  
```



### 2.error: assigning to 'char \*' from incompatible type 'void \*'

报错出现在上述代码的_pstr = malloc(size + 1);这里，主要原因是C对于类型转换是很宽泛的，但是C++不行malloc返回的是void \*，这里赋值到char \*需要类型的转换。

```C++
s->ptr = static_cast<char*>(malloc(s->len+1));
```



### 3.malloc 与 new

> a.malloc是C的一个库函数，new是C++中的操作符。

> b.malloc只是分配了一块内存而不是生成一个对象，它返回了一个void\*类型指针。

对于非内部数据类型的对象而言，光用malloc/free无法满足动态对象的要求。对象在创建的同时要自动执行构造函数，对象在消亡之前要自动执行析构函数。由于malloc/free是库函数而不是运算符，不在编译器控制权限之内，不能够把执行构造函数和析构函数的任务强加于malloc/free。

因此C++的解决方案是创建一个对象所需的所有动作都结合在一个称为new的运算符里。当用new创建一个对象时，它就在堆里为对象分配内存并为这块内存调用构造函数。

> c.delete表达式首先调用析构函数，然后释放内存(经常调用free)。

正如new表达式返回一个指向对象的指针一样，delete表达式需要一个对象的地址。

> d.C++中int a[10]和int a=new int[10]有什么区别

前者固定大小，后者动态分配。前者在栈上，后者在堆上。

**如果不是a[10]，而是a[1000000000\]或者更大的话，那一般情况下，就只能使用int* a=new这种方式了。这个涉及到内存存放位置的问题，int a[\]这种方式，内存是存放在栈上；int* a＝new int[\]这种方式，内存是存放在堆上，栈的实际内存是连续内存，因此可分配空间较小，堆可以是非连续内存，因此可以分配较大内存。因此，如果需要分配较大内存，需要分配在堆上**

> e.new int[10] 与 new int[10\]()区别

int *pia = new int[10];    // 10个未初始化int

int *pia2 = new int[10\](); // 10个值初始化为0的int

int *p2 = new int[10\]();申请了空间，而且进行了初始化。int *p1 = new int[10\];只申请空间，没有进行初始化原因：对于一些结构体，我们可以看到（）往往表示构造函数，int是基本类型初始化。理由：你可以测试输出两种的值你会发现p1的值未知，而p2清零了。

new运算符只是申请分配一个内存空间而已，因为不知道为其分配对象的名称，所以分配之后返回的只是一个指向该对象的指针，并没有初始化，加上一个（）后，就相当于调用了默认构造函数，会默认初始化，用0来初始化.



### 4.union联合

union的所有成员引用的是**内存中的相同位置**，当你想在不同的时刻把不同的东西存储于同一个位置时，就可以使用联合。

```C++
union
{
  float f;
  int i;
}fi;
fi.f = 3.14159;
printf("%d\n",fi.i);
```