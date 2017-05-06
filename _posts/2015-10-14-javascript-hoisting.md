---
layout: post
title: "Javascript:变量声明提升(hoisting)"
categories: 前端开发
tags: javascript
---

与其他语言不同， javascript 的变量是可以先使用再声明的，函数一是可以先使用再定义（后面统一称为函数声明）的。先不讨论 ES6 里面的 let 和所有入门教程里所说的变量可以不声明可以直接使用。一段代码可以这样写

{% highlight javascript linenos=table %}
fn(1);
function fn(a)
{
    b = 10;
    console.log(a,b);
    var b;
}
{% endhighlight %}

上述代码不会产生任何的异常，也不会生成全局变量 `b`。 在这背后起作用的是一种叫做变量声明提升(hoisting)的机制。这个机制主要是说， Javascript 引擎在执行代码的时候会把所有的变量和函数声明提升到当前作用域的最前面。`变量声明提升`是说， **JS解释器在执行一个代码块之前，首先会扫描这一段代码以确定和定义在这个代码块中使用的变量，然后移除代码中所有的变量和函数声明后再执行代码**。就是说无论变量声明在哪里，都相当于在最前面声明了这个变量。但是这种提升对函数声明和变量声明的处理也不同。并且 Javascript 里面变量可以重复声明，形参可以重名，函数可以重复声明。如果重复声明和变量声明提升同时出现结果又是怎样的呢？

### 1. 变量声明提升

Javascript 引擎在执行代码的时候会把所有的函数声明和提升到当前作用域的最前面，实际执行的代码是不包含任何函数定义和变量声明的。

#### 1.1. 变量 

{% highlight javascript linenos=table %}
var a=1;
function fn()
{
    console.log(a); // undefined
    var a = 2;
    var a;
    console.log(a); // 2
}
fn();
{% endhighlight %}

在函数中，第一次输出 `a` 并没有使用的全局变量`a`,而是本作用域中后面声明的`a`,但是由于没有给其赋值，所以是其值为`undefined`。Javascript 只有变量声明和变量赋值，还不存在变量初始化这个概念。`var a = 2;` 其实是声明变量，然后赋值为`2`的意思。第二次输出 `a` 并没有输出 `undefined`，因为通过变量声明提升，无论是第一次声明还是第二次声明这两次声明都在整个作用域的最前面（其实会忽略第二次声明）。 所以说第二次声明并不会导致`a = undefined`。其实上述代码相当于

{% highlight javascript linenos=table %}
var a=1;
function fn()
{
    var a;
    // var a;  // 对于变量这个声明其实是被忽略的
    //===========被提升的部分===============
    console.log(a); // undefined
    a = 2;
    console.log(a); // 2
}
fn();
{% endhighlight %}

结论，对于变量，所有变量声明会被提升到本作用域的最前面，重复的声明则忽略，初始值`undefined`。

#### 1.2. 函数

同样对于函数也存在变量声明提升的情况。

{% highlight javascript linenos=table %}
function  f() { return "Global";}
function fn()
{
    console.log(f());  // last
    function f(){ return "first"; }
    function f(){ return "second"; }
    function f(){ return "last"; }
    // var f = function () { return "Anonymous function"; }
}
fn();
{% endhighlight %}

首先，最后一行注释掉的代码，并不是函数声明，仅仅一个生成一个匿名函数，赋值给`f`.后面也会提到，如果去掉注释，执行结果不变。对于函数声明，Javascript 依然会进行变量声明提升，而且会给其赋一个初始的值。那么这就涉及这个初始值是多少的问题了：这个初始值其实是最后一次声明的那个函数。所以程序输出是last。

结论，函数声明也会被提升，初始值为最后一次声明的那个函数。

### 2. 变量重名

#### 2.1 形参重名


{% highlight javascript linenos=table %}
function fn(a,a,a)
{
    console(a);
}
fn(1,2,3); // 输出 3
fn(1,2);   // 输出 undefined
fn(1);     // 输出 undefined
{% endhighlight %}

可见，在函数中形参的的初始值是最右边的形参对应的实参值，如果实参不存在则为`undefined`

### 2.2 函数与形参重名

{% highlight javascript linenos=table %}
function fn(a)
{
    function a() {}
    console.log(typeof a); // function
}
fn(1);
{% endhighlight %}

可见函数与形参重名的时候变量的初始值是函数。

### 2.3 一个形参是"arguments"

这里的 `arguments` 指的是函数中的那个特殊变量:`arguments`

{% highlight javascript linenos=table %}
function fn(arguments)
{
    console.log(arguments); // 1
}
fn(1);
{% endhighlight %}

可见一个参数是 `arguments` 那么`arguments` 的初始值是对应的那个实参

### 2.4 在变量中声明一个 "arguments"

{% highlight javascript linenos=table %}
function fn()
{
    var arguments;
    console.log(typeof arguments); // object
}
fn();
{% endhighlight %}

可见 `arguments` 的初始值就是在 Javascript 中定义的那个，而不是重新声明的那个。

### 2.5 多个重名的问题

通过上面的的实验，可以知道 Javascript 引擎在执行代码的时候会把所有的函数声明和变量声明提升到本作用域的最开始,而且变量可以重复声明。重名时初始值的优先级顺序是：**函数声明>形参**， **形参>arguments**， **arguments>变量声明** 。那么当多个变量重名的时候其初始值的优先级是不是符合**函数声明 > 形参 > arguments > 变量声明**？ 答案是：是。这里就不证明了。而且变量声明后初始值就是 `undefined` 所以说其实有初始值的变量就是那些函数，形参，`arguments`。除此之外，其他变量都是`undefined`。

### 3. 结论

总结一下，在 javascript 执行函数中的代码时，会将所有的变量声明和函数定义（指`function fn() { code; }`这种写法，匿名函数不算）都会被从函数代码中移除，然后执行函数代码。其中所有局部变量的初始值按照如顺序解析(或者说优先级)：

1. 在代码中最后一次定义的一个函数;
2. 最右边的一个形参;
3. 特殊变量`arguments`;
4. 其他声明的变量，都为`undefined`。

此外， IE 有一个大的 BUG [参考这里](http://hax.iteye.com/blog/349569)

### 4. 进阶

对于有兴趣的人，可以参考如下 ECMA-262 5.1 标准。只涉及有关变量声明提升的部分才有详细的解释，且不包括一些特殊的情况在 **ES5.1** 里面函数执行过程如下：

1. **确定“this”的值**   
(确切的来说，`this`在JS里面不是一个变量名而是一个关键字)

1.  **创建一个新的作用域为当前作用域**

1.  **声明形参，并赋值为对应的实参（没有声明过才声明，无论如何都重新赋值，没有对应实参则赋值为"undefined"）：**    
对于每一个形参，按照从左往右的顺序依次执行：如果对应的形参在本作用域中还没有声明，则在本作用域中声明形参，并赋值为对应的实参。如果已经声明过了，则重新给其赋值为对应的实参。(没有对应实参则赋值为"undefined"）

1. **声明函数（没有声明过才声明，无论如何都重新赋值为当前对应函数）：**    
对该函数中所有的定义的函数，按照代码写的顺序依次执行：如果这个变量名在本作用域中还没有声明过，则在本作用域中声明这个函数名，并且赋值为对应的函数。如果定义了这个变量且可一重新赋值的情况下重新给这个变量赋值为对应函数，否则抛出异常。

1. **声明 "arguments"（没有声明过才声明和赋值）:**   
如果在本作用域中没有声明`arguments`，则在本作用域中声明`arguments`并给其赋值。

1. **声明所有局部变量（没有声明过才声明，不赋值）：**   
对于所有变量声明，按照代码写的顺序依次执行：如果在本作用域中没有声明过这个变量，则在本作用域中声明这个变量，赋值为`undefined`

1. 移除所有的变量和函数声明，执行代码。

参考：<http://www.ecma-international.org/ecma-262/5.1/#sec-10.5>


