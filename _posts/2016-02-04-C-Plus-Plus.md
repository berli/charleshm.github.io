---
published: true
author: Charles
layout: post
title:  "C++基础札记"
date:   2016-02-04 15:30
categories: 数据结构与算法
---

#### 初始化
![此处输入图片的描述][1]

一般情况下，初始化可以用**花括号，圆括号，等号**这三种方式。作为 C++11 新标准的一部分，用花括号来初始化变量得到了全面应用。无论是初始化对象还是某些时候为对象**赋新值**，都可以使用这样一组由花括号起来的**初始值**了。

<pre class="prettyprint linenums">
int a = 0;
int a = {0};
int a(0);
int a{0};
</pre>

> 初始化不是赋值，初始化的含义是创建变量时赋予其一个初始值，而赋值的含义是把对象当前的值擦除，以一个新值来代替。

另外定义变量的时候，如果没有指定初始值，则变量被**默认初始化**(建议初始化每一个内置类型的变量)。一种例外的情况是定义在**函数体内部**的内置类型变量将**不被初始化**。


----------


#### 变量声明与定义
- 声明使得名字为程序所知，而定义负责创建于名字关联的实体。
- 变量声明规定了变量的**类型和名字**，这一点上定义与之相同，但定义同时还申请了**存储空间**。
- 变量能且只能被**定义一次**，但是却可以被**多次声明**。

<pre class="prettyprint linenums">
extern int i;                  //声明 i 而非定义 i
int j;                         //声明并定义 j
extern double pi = 3.1416;     //定义
</pre>


----------


### 引用与指针

#### 声明的两种写法

涉及到指针或引用的声明，一般会有**两种写法**：

<pre class="prettyprint linenums">
int *p; &r    //把修饰符(* 或 &)与变量名写在一起

int* p;
int& r;      //把修饰符和类型名写在一起
</pre>

这两种写法并**没有孰对孰错**之分，关键在于从一而终。


----------


#### 引用
> 引用并非对象，它只是为一个已经存在的对象去的**别名** .

**引用与指针的区别**，

- 指针是一个**实体**，而引用仅是个**别名**；
- 引用在定义时**必须初始化**（谁的别名），之后**不能改变**(从一而终)；指针可变；
- 引用**不能为空**，必须与合法的存储单元关联；指针可以为空；

<pre class="prettyprint linenums">
int &refVal;       //报错，引用必须初始化

int ival = 42;
int *p = &ival;
int *q = ival;     //错误： 不能把 int 变量直接赋值给指针
</pre>

----------

#### 指针赋值

在学指针时，我们常常会犯这样的错误，

<pre class="prettyprint linenums">
int *p = 10;
</pre>

因为经常听到的一句话就是，**指针就是地址**，那么为什么不能用常量或 int 变量给指针赋值？

这样的理解是不准确的，指针是一种**变量**，存放着某个对象的地址，它有自己的**类型**，比如上面的 int *。我们要给其赋值，那也得是赋 int * 的，

<pre class="prettyprint linenums">
int *p = (int *)10;    //编译通过，但会引发访问权限异常
</pre>


----------

#### 空指针

**空指针**，

<pre class="prettyprint linenums">
int *p1 = nullptr;    //等价于 int *p1 = 0;
</pre>

----------

#### 复合类型类型

- **指向指针的指针**

<pre class="prettyprint linenums">
int ival =1024;
int *pi = &ival;     // pi 指向一个 int 型的数
int **ppi = &pi;     // ppi 指向一个 int 型的指针
</pre>

- **指向指针的引用**

<pre class="prettyprint linenums">
int i= 42；
int *p;
int *&r = p;    //r 是对指针 p 的引用

r = &i;
*r = 0;        //解引用 r 得到 i，也就是 p 指向的对象
</pre>

> 面对比较复杂的指针或引用的声明语句时，最简单的办法就是从右往左读，**离变量名最近**的符号对变量名的类型有**最直接的影响**。

----------

#### const 限定符

因为 const 对象一旦创建后就不容许改变，所以必须初始化。

<pre class="prettyprint linenums">
const int j = 42;
const int k;         //错误: k是一个未经初始化的常量
</pre>

>  指向常量的引用（指针），没有规定其所指向的对象必须是一个常量，所谓指向常量的引用（指针），它们觉得自己指向了常量，所以自觉地不去更改所指对象的值，而并不是说所指对象的值不能通过其它方式改变。

<pre class="prettyprint linenums">
int i = 42;
const int &r = i;  //指向常量的引用
const *ptr = &i;   //指向常量的指针
</pre>

  [1]: http://7xjbdi.com1.z0.glb.clouddn.com/c++_ini.png?imageView/2/w/250