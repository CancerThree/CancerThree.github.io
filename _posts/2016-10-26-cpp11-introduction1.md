---
layout: post
title: C++ 11新特性介绍（一）
description: C++ 11
author: Mumoo
modified: 2016-10-26 22:30:13 +0800
categories: C++
tags: C++
---

本篇博客介绍一下C++11的j常用新特性，主要来源与《C++ Primer 5th》以及网上的博客。作为一个记录整理，方便自己日后查阅。

<!-- more -->

#### 变量和基本类型

##### 列表初始化

在初始化的时候，任何数据类型，都可以用花括号来进行初始化。

1. 基本数据类型的初始化

{% highlight cpp %}
int a{110};
int a = {110};
{% endhighlight %}

2. 结构体初始化
结构体在使用列表初始化的时候，当结构体没有指定构造函数的时候，就按照C风格来进行初始化，当有构造函数的时候，就调用构造函数进行初始化

{% highlight cpp %}
struct initTestStruct
{
	int a;
	int b;
	initTestStruct(int x, int y) { a = x; b = x - y; };
};
struct initTestStruct initS{1, 2};  //a = 1, b = -1
{% endhighlight %}

3. 容器初始化

{% highlight cpp %}
vector<string> s_v{"str0", "str1", "str2"};
{% endhighlight %}

4. 其余

其余还有一些初始化，但都类似，不做一一介绍。

##### nullptr常量

以下几种初始化方式都是等价的：

{% highlight cpp %}
int* p1 = 0;
int* p2 = NULL; //NULL其实是0的宏：#define NULL 0
int* p3 = nullptr;
{% endhighlight %}

##### 别名声明

C++11提供了使用using来进行别名声明。以下的两种方式等价

{% highlight cpp %}
typedef int int32;
using int32 = int;
{% endhighlight %}

##### auto关键字

这是C++ 11的一个重大改进，使用该关键字可以让编译器通过表达式推导来替我们分析变量的类型。

{% highlight cpp %}
//基础类型：
auto a;     //无法使用此种类型的声明，因为没有初始化表达式
auto a = 1; //正确

//函数：
auto func = funcName();

//容器
vector<string> s_v;
auto ite = s_v.begin();

//类
auto c = new myClass();
{% endhighlight %}

#### 语句

##### 范围for语句

和其他语言类似，一个很棒的语法糖，直接看例子。（终于支持这个功能了，撒花~）

语法形式：expression可以是数组、容器等能返回迭代器的对象。
{% highlight cpp %}
for (declaration : expression)
    statement
{% endhighlight %}

结合auto关键字可以很方便地进行遍历
{% highlight cpp %}
vector<int> v = {1, 2, 3, 4};

for (auto ite : v)
    std::cout << *ite._Ptr << std::endl;

//与其等价的传统遍历方式
for (auto b = v.begin(), e = v.end(); b != e; b++)
	std::cout << *b._Ptr << std::endl;
{% endhighlight %}

#### C++的lamda表达式

C++ 11中加入了lamda表达式，在一定程度上，使用lamda表达式可以简化程序，使得代码更加清晰。可以理解为一个匿名函数。lamda表达式形式如下：

> [captureList](parameterList)mutable->returnType {statement}

1. captureList: 捕捉列表。捕捉列表能够捕捉上下文中的变量以供Lamda函数使用。

2. parameterList: 与普通函数相同，若不需要参数，可以省略。

3. mutable: 修饰符。默认情况下，Lamda函数是const函数，使用mutable修饰的化，可以取消其常量性，其函数代码改变被捕获的值。使用该修饰符时，参数列表不可省略。

4. returnType: 返回类型。在不需要返回值时可以与'->'一起省略。

5. statement: 和普通函数一样，只是多了可以使用捕获的变量。

Lamda的捕捉列表可以有以下几种:

> 1. [a] 表示以值传递的方式捕捉变量a;
> 2. [=] 表示已值传递的方式捕捉所有父作用域的所有变量（包括this）;
> 3. [&a] 表示以引用传递的方式捕捉变量a;
> 4. [&] 表示以引用传递的方式捕捉所有父作用域的所有变量（包括this）。

捕捉列表可以组合使用，但是在组合使用的时候，不允许重复捕捉，比如说：
> [=, a] 这里已经用值传递的方式捕捉了所有变量，不允许再使用值传递来捕捉a了。

##### eg1. mutable关键字

{% highlight cpp %}
	int testVal = 1;

	[=] {testVal++; };  //编译会报错，testVal不可改变的

	[=]()mutable->void{testVal++; };    //编译正确，可以使用testVal值
{% endhighlight %}

##### eg2.值捕捉与引用捕捉

{% highlight cpp %}
	int testVal = 1;

	[=]()mutable->void{testVal++; };    //在该Lamda表达内，testVal被改变为2
    std::cout << testVal << std::endl;  //由于是值捕捉，父作用域中testVal=1

	[&] {testVal++; };  //引用捕捉中，表达式内的改变可以影响父作用域
    std::cout << testVal << std::endl;  //由于是引用捕捉，父作用域中testVal被改变，=2
{% endhighlight %}

##### eg3.Lamda对程序的简化
看一个例子，比如在使用sort函数进行排序的时候，需要先定义一个排序的规则函数，然后再调用sort。但是可能这个规则函数只用一次。
{% highlight cpp %}
bool compare(int a, int b)
{
	return a > b;
}

//省略一下调用sort前的函数
int testArr[]{1, 2, 3, 4, 5};

std::sort(aatestArraa, testArr + 5, compare);
{% endhighlight %}

改成Lamda可以是这样：
{% highlight cpp %}
int testArr[]{1, 2, 3, 4, 5};

std::sort(aatestArraa, testArr + 5, [](int a, int b)->bool {return a > b;});
{% endhighlight %}

直接简化了代码，并且少了一个函数的命名，减少了对命名空间的污染（才不是偷懒~）。