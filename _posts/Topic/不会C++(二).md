---
title: 不会C++(二)(继承和组合)
date: 2017-6-30 15:17:55
tags: [C++]
categories: [C++]
---

对于知识上的匮乏到了忍无可忍的地步。。。需要认真**从头**梳理一些概念的理解。

不把这些搞清楚，实在没有办法安心做其他的。自己怎么这么弱啊。。。

<!--more-->

《Thinking in C++》第十四章一上来是这么说的：

> C++最重要的特性之一是代码重用，但是如果希望更进一步，就不能仅仅用拷贝代码和修改代码的方法，而是要做更多的工作。
>
> 在本章中，我们将看到两种完成这项任务的方法：第一种方法很直接：我们简单地在新类中创建已存在的类的对象。因为新类是由已存在类的对象组合而成，所以这种方法称为组合(*composition*)。
>
> 第二种方法要复杂些。我们创建一个新类作为一个已存在类的类型。我们不修改已存在的类，而是采取这个已存在类的形式，并将代码加入其中。这种方法称为继承(*inheritance*)。



#### 1.组合语法

```C++
class X
{
	int i;
public:
	X(){i=0;}
	void set(int ii){i=ii;}
	int read()const {return i;};
	int permute(){return i=i+47;}
};

class Y
{
	int i;
public:
	X x;
	Y(){i=0;}
	void f(int ii){i=ii;}
	int g()const {return i;}
};

int main()
{
	Y y;
	y.f(47);
	y.x.set(37);
	return 0;
}
```

更常见的是把嵌入的对象设为私有，因此它们将成为内部实现的一部分。

```C++
class Y
{
	int i;
public:
	X x;
	Y(){i=0;}
	void f(int ii){i=ii;x.set(ii);}
	int g()const {return i*x.read();}
	void permute(){x.permute();};
};

int main()
{
	Y y;
	y.f(47);
	y.permute();
	return 0;
}
```



#### 2.继承语法

我们规定，在代码中国中和原来一样给出该类的名字，但在类的左括号的前面，加一个冒号和基类的名字(对于多重继承，要给出多个基类名，它们之间用逗号分开)。当做完这些时，将会自动地得到基类中的所用数据成员和成员函数。



```C++
class Y:public X
{
	int i;
public:
	Y(){i=0;}
	int change()
	{
		i = permute();
		return i;
	}
	void set(int ii)
	{
		i = ii;
		X::set(ii);
	}
};

int main()
{
	cout << "sizeof(X) = " << sizeof(X) << endl;
	cout << "sizeof(Y) = " << sizeof(Y) << endl;

	Y D;
	D.change();
	D.read();
	D.permute();
	D.set(12);
	return 0;
}
```



Y对X进行了继承，**这意味着Y将包含X中的所有数据成员和成员函数。**

所有X中的私有成员在Y中仍然是私有的，这是因为Y对X进行了继承并不意味着Y可以不遵守保护机制。**X中的私有成员仍然占有存储空间，只是不可以直接地访问它们。**

另外，**在继承时，基类中所有的成员都是被预设为私有的，所以如果基类的前面没有public，这意味着基类的所有公有成员将在派生类中变为私有的。这显然不是所希望的**，我们希望基类中的所有公有成员在派生类中仍是公有的。这可以在继承时通过关键字public来实现。

当我们重新定义了一个函数之后，仍可能想调用基类的函数。但如果对于set()，只是简单地调用set()函数，将得到这个函数的本地版本，一个递归的函数调用，**为了调用基类的set()函数，必须使用作用域运算符来显式地标明基类名。**

#### 3.构造函数的初始化表达式表

对于子对象调用构造函数，C++为此提供了专门的语法，即构造函数的初始化表达式表。构造函数的初始化表达式表的形式模仿继承。对于继承，我们把基类至于冒号和这个类体的左括号之间。而在构造函数的初始化表达式表中，可以将对子对象构造函数的调用语句放在构造函数参数表和冒号之后，在函数体的左括号之前。对于从Bar继承来的类MyType，如果Bar的构造函数只有一个int型参数，则可以表示为：

```C++
MyType::MyType(int i):Bar(i){}
```

3.1成员对象初始化

显然对于组合，也可以对成员对象使用同样语法，只是所给出的不是类名，**而是对象的名字**。如果在初始化表达式表中有多个构造函数的调用，应当用逗号加以隔开。

```C++
MyType2::MyType2(int i):Bar(i),m(i+1){}
```

3.2在初始化表达式表中的内建类型

为了使语法一致，可以把内建类型看作这样一种类型，它只有一个取单个参数的构造函数，而这个参数与正在初始化的变量的类型相同。于是，可以这样写：

```C++
class X
{
	int i;
	float f;
	char c;
	char *s;
public:
	X():i(7),f(1.4),c('x'),s("howdy"){}
};

int main()
{
	int i(100);
	int *ip=new int(47);
	return 0;
}
```

这使得内建类型的操作有点类似对象。但要记住，这些并不是真正的构造函数。特别地，如果没有显式的进行伪构造函数调用，初始化是不会执行的。

#### 4.组合和继承的联合

虽然常常需要在初始化表达式表中做显式构造函数调用，但并不需要做显式的析构函数调用，因为对于任何类型只有一个析构函数，并且它并不取任何参数。然而，编译器仍要保证所有的析构函数被调用，**这意味着，在整个层次中的所有析构函数中，从派生最底层的析构函数开始调用，一直到根层。**

4.1构造函数和析构函数调用的次序

```c++
#define CLASS(ID) class ID{\
public:\
ID(int){ cout << #ID " constructor\n";}\
~ID(){ cout << #ID " destructor\n";}\
};

CLASS(Base1);
CLASS(Member1);
CLASS(Member2);
CLASS(Member3);
CLASS(Member4);


class Derived1:public Base1
{
	Member1 m1;
	Member2 m2;
public:
	Derived1(int):m2(1),m1(2),Base1(3)
	{
		cout << "Derived1 constructor\n";
	}
	~Derived1()
	{
		cout << "Derived1 destructor\n";
	}
};

class Derived2:public Derived1
{
	Member3 m3;
	Member4 m4;
public:
	Derived2():m3(1),Derived1(2),m4(3)
	{
		cout << "Derived2 constructor\n";
	}
	~Derived2()
	{
		cout << "Derived2 destructor\n";
	}
};

int main()
{
	Derived2 d2;
	return 0;
}
```

这个程序的输出是：

```
Base1 constructor
Member1 constructor
Member2 constructor
Derived1 constructor
Member3 constructor
Member4 constructor
Derived2 constructor
Derived2 destructor
Member4 destructor
Member3 destructor
Derived1 destructor
Member2 destructor
Member1 destructor
Base1 destructor
```

可以看出，**构造是从类层次的最根处开始，而在每一层，首先会调用基类构造函数，然后调用成员对象构造函数。调用析构函数则严格按照构造函数相反的次序。**这是很重要的，因为要考虑潜在的相关性(对于派生类中的构造函数和析构函数，必须假设基类子对象仍然可供使用，并且已经被构造了，或者还未被消除)。

另一个有趣现象是，**对于成员对象，构造函数调用的次序完全不受构造函数的初始化表达式表中的次序影响。该次序是由成员对象在类中声明的次序所决定的**。如果能通过构造函数的初始化表达式表改变构造函数调用次序，那么就会对两个不同的构造函数有两种不同的调用顺序，而析构函数将不能知道如何相应逆序地执行析构，这就产生了相关性问题。

#### 5.名字隐藏

```C++
class Base
{
public:
	int f()const 
	{
		cout << "Base::f()\n";
		return 1;
	}
	int f(string)const 
	{
		return 1;
	}
	void g()
	{

	}
};
class Derived1:public Base
{
public:
	void g()const{}
};

class Derived2:public Base
{
public:
	int f()const
	{
		cout << "Derived2::f()\n";
		return 2;
	}
};

class Dervied3:public Base
{
public:
	void f()const 
	{
		cout << "Dervied3::f()\n";
	}
};

class Dervied4:public Base
{
public:
	int f(int)const
	{
		cout << "Dervied4::f()\n";
		return 4;
	}
};

int main()
{
	string s("hello");
	Derived1 d1;
	int x = d1.f();
	d1.f(s);

	Derived2 d2;
	x = d2.f();
	//! d2.f(s);

	Dervied3 d3;
	d3.f();
	//! x = d3.f();

	Dervied4 d4;
	//! x = d4.f();
	x = d4.f(1);

	//system("pause");
	return 0;
}
```

在Base类中有一个可被重载的函数f()，类Derived1并没有对函数f()进行任何改变，但它重新定义了函数g()，在main()中，可以看到函数f()的两个重载版本在类Derived1中都是可以使用的。但是由于类Derived2重新定义了函数f()的一个版本，而对另一个版本没有进行重定义，因此这第二个重载形式是不可以使用的。在类Derived3中，通过改变返回类型隐藏了基类中的两个函数版本，而在类Derived4中，通过改变参数列表同样隐藏了基类中的两个函数版本。总体上，可以得出，**任何时候重新定义了基类中的一个重载函数，在新类之中所有其他的版本则被自动地隐藏了。在下一篇博文中，将会看到加上virtual这个关键字会对函数的重载有一点影响。**



#### 6.非自动继承的函数

不是所有的函数都能自动地从基类继承到派生类中的。构造函数和析构函数用来处理对象的创建和析构操作，但它们只知道对它们的特定层次上的对象做些什么。所以，在该类以下各个层次中的所有构造函数和析构函数都必须被调用，也就是说，**构造函数和析构函数不能被继承，必须为每一个特定的派生类分别创建。**

另外，operator=也不能被继承，因为它完成类似于构造函数的活动。

```C++
class GameBoard
{
public:
	GameBoard(){cout << "GameBoard()\n";}
	GameBoard(const GameBoard&)
	{
		cout << "GameBoard(const GameBoard&)\n";
	}
	GameBoard& operator=(const GameBoard&)
	{
		cout << "GameBoard::operator=()\n";
		return *this;
	}
	~GameBoard(){cout << "~GameBoard()\n";}
};

class Game
{
	GameBoard gb;
public:
	Game(){cout << "Game()\n";}
	Game(const Game &g):gb(g.gb)
	{
		cout << "Game(const Game&)";
	}
	Game(int){cout << "Game(int)\n";}
	Game& operator=(const Game& g)
	{
		gb = g.gb;
		cout << "Game::operator=()\n";
		return *this;
	}
	class Other
	{
	public:
		Other(){cout << "AC!" << endl;}
	};
	operator Other()const
	{
		cout << "Game::operator Other()\n";
		return Other();
	}
	~Game(){cout<<"~Game()\n";}
};

class Chess:public Game{};
void f(Game::Other){}
class Checkers:public Game
{
public:
	Checkers(){cout << "Checkers()\n";}
	Checkers(const Checkers &c):Game(c)
	{
		cout << "Checkers(const Checkers &c)\n";
	}
	Checkers& operator=(const Checkers &c)
	{
		Game::operator=(c);
		cout << "Checkers::operator=()\n";
		return *this;
	}
};

int main()
{
	Chess d1;
	Chess d2(d1);
	d1=d2;
	f(d1);
	Game::Other go;
	Checkers c1,c2(c1);
	c1=c2;
	//system("pause");
	return 0;
}
```

运行结果：

```
GameBoard()
Game()
GameBoard(const GameBoard&)
Game(const Game&)GameBoard::operator=()
Game::operator=()
Game::operator Other()
AC!
AC!
GameBoard()
Game()
Checkers()
GameBoard(const GameBoard&)
Game(const Game&)Checkers(const Checkers &c)
GameBoard::operator=()
Game::operator=()
Checkers::operator=()
~Game()
~GameBoard()
~Game()
~GameBoard()
~Game()
~GameBoard()
~Game()
~GameBoard()
```

这里面有几个点需要注意：

operator Other()从Game对象到被嵌入的类Other的对象完成自动类型变换。

Checkers中，它显式地写了默认构造函数，拷贝构造函数和赋值运算符。**在默认构造函数中，默认的基类构造函数被自动地调用，这正是我们所希望的**。但是，**一旦决定写自己的拷贝构造函数和赋值运算符，编译器就会假定我们已知道所做的一切，并且不再像在生成的函数中那样自动地调用基类版本。而如果想调用基类版本，那我们就必须亲自显式地调用它们**。Checkers的拷贝构造函数中，这个调用出现在构造函数的初始化列表中：

```C++
Checkers(const Checker &c):Game(c){}
```

至于Checkers赋值运算符，基类的调用在函数体的第一行中：

```C++
Game::operator=(c);
```

#### 7.子类型设置

现在假设想创建ifstream对象的一个类，它不仅打开一个文件，而且还保存文件名，这时可以使用组合并把ifstream及string都嵌入这个新类中：

```c++
class FName1
{
	ifstream file;
	string fileName;
	bool named;
public:
	FName1():named(false){}
	FName1(const string& fname):fileName(fname),file(fname.c_str())
	{
		named = true;
	}
	string name()const{return fileName;}
	void name(const string& newName)
	{
		if(named)return;
		fileName = newName;
		named = true;
	}
	operator ifstream &()
	{
		return file;
	}
};
int main()
{
	FName1 file("FName1.cpp");
	cout << file.name() <<endl;
	return 0;
}
```

这里ifstream &是类型转换的重载。 [链接](http://www.cnblogs.com/fornever/p/3443539.html)

声明：operator XX();无返回值，XX就是某个类型。

用法：XX a = (XX)obj;

然而这里存在一个问题，我们也许想通过包含一个从FName1到ifstream &的自动类型转换，在任何使用ifstream的地方都使用FName1对象，但在main中，

```
file.close();
```

其中一个方法是对FName1增加close()的定义：

```
void close(){file.close();}
```

如果只有很少的函数要从ifstream类中拿来，这是可行的。在这种情况下，我们只是用了这个类的一部分，并且组合是适用的。但是，如果希望这个类中的每个函数都具有，这就是必须使用继承的地方。

```C++
class FName2:public ifstream
{
	string fileName;
	bool named;
public:
	FName2():named(false){}
	FName2(const string &fname):ifstream(fname.c_str()),fileName(fname)
	{
		named=true;
	}
	string name()const{return fileName;}
	void name(const string &newName)
	{
		if(named)return;
		fileName = newName;
		named = true;
	}
};

int main()
{
	FName2 file("FName1.cpp");
	cout << file.name() <<endl;
	string s;
	getline(file,s);
	file.seekg(-200,ios::end);
	file.close();
	return 0;
}
```

现在，能与ifstream对象一起工作的任何成员函数也能与FName2对象一起工作。需要使用ifstream对象的非成员函数，例如getline()，同样可以使用FName2对象。

















