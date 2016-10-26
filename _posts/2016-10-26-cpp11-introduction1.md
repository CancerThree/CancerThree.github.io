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