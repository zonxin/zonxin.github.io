---
layout: post
title: "我的Monad教程-1:编程就是函数复合"
categories: 前端开发
tags: javascript 单子 Monad 设计模式
---

不得不说，LISP 和 Haskell 这样的语言正在极大的影响着各种编程语言。今天我们主要来看一下 Haskell 中的单子是个什么东西。Haskell 基于范畴论，范畴论属于抽象数学。而抽象的魅力在于可以把很多看似不相关的东西解释成同一个东西，既然是同一个东西，我们就可以使用同样的的代码来完成看似不同的任务。这样同样一段代码出现的机会就更大，就可以通过规定语法的方式来减小代码量。比如在Javascript 中常用的一个形式是`(function (x){return x;}).bind(this)`,所以ES2015定义了新的语法，上述代码可以写为`x => x`。短的代码书写迅速，不宜出错，调试方便。

网上单子的教程都是转来转去——单子类似于一个盒子。不得不说这种比喻真的很对，可是似乎对学习单子帮助不大。如果只是想了解一下[这些东西][MONADINPIC]还是很值得推荐的。Haskell 中的单子等价于数学上定义的单子，可是数学总是过于抽象的，现在我们来看一下具体到编程里该如何去理解单子。来看一看 Haskell 对 Javascript 的影响是什么(或许着不是Haskell影响，而是广大的Javascript程序员们自己总结出来的设计模式，但是不能不说这种处理思想越来越趋于 Haskell)。

### 1. 编程就是函数复合

<p class="block-image"><img alt="函数复合" src="/image/monad/compose.png" /><br />函数复合</p>

编程就是编写代码处理"我们关心的数据"以产生"我们想要的数据"。通常这是一个比较复杂的函数，需要把这个大函数拆分成更小的函数，然后把这些函数复合到一起组合成最终的这个大函数。编程语言提供了基本的函数，比如加法，减法等。因此 **编程就是把一个个的函数复合到一起组成一个更大的函数去处理"我们关心的数据"** 。程序员的精力应该更多的放在 fn1~fn4 的实现上，而不是如何去复合这些函数。一个好的“模式”，就应该能够让程序员方便的复合这些函数。因此，作为程序的一小部分，fn2 只关心 x 是什么，它要完成的任务是什么，而不用关心程序的其他部分。对 fn2 来说，x 就是"我们关心的数据"。

函数的复合方式有很多中，单子就是着其中的一种。单子成功的分离了"我们关心的数据"以及"我们不关心的实现"——那些书上一直教育我们的。而且"我们不关心的实现"是一个更加的通用的代码，"我们关心的数据"获取更加方便。"我们不关心的实现" 这种复合方式的内部实现，我们真的不用关心(可是设计者为了给我们留特别简单的接口就需要非太多的心思)。与其说这是单子不如说是设计模式，一种特别通用的设计模式，一种 Java 中暂时还不存在的设计模式。

### 2. 基本函数复合

最简单的函数复合类似于数学上的函数复合，比如:
{% highlight javascript linenos %}
function compose(f,g) {
    return function(x){ return f(g(x)); };
    // 注意参数顺序，先调用 g ;为了和 f(g(x)) 顺序一样所以这样规定
    // 渐渐的我会写为ES2015的箭头函数形式
    // return x => f(g(x));
}
//===================================================
function father(name) {
    // 获取一个人的父亲...代码略
    return name + "'s father";
}
function mother(name){
    // 获取一个人的母亲...代码略
    return name + "'s mother";
}
// 获取一个人祖母
var fn = compose(mother,father); 
var grandma = fn("Peter");
// 或更常见的写法
var dad = father("Peter");
var grandma = mother(dad);
{% endhighlight %}
这是最简单的最基本的函数复合方式，到处都可以见到类似的代码，就不赘述了。如果涉及到错误处理还需要判断`father`和`mother` 是否能够正确的执行，实际写的代码会更加的复杂。写这样的代码仅仅体力劳动，枯燥而且特别容易出错，尤其是代码更长的时候：
{% highlight javascript linenos %}
function grandgrandma(name)
{
    var dad = father(name);
    if(!dad) { return ""; } // 假设返回空表示错误
    var grandpa = father(dad);
    if(!grandpa) { return ""; }
    return mother(grandpa);
}
{% endhighlight %}
这段代码中有好多讨厌的`if`，那如果把错误处理的代码放到 `compose` 中呢？这样就不需要再在 `compose` 外面写错误处理了(为了更通用写的复杂了一点儿):
{% highlight javascript linenos %}
const ERROR="";
function compose(f,g){
    var args = [].slice.call(arguments);
    return function(x) {
        var t=x,i;
        for(i=args.length-1;i>=0;i--)
        {
            t = args[i].call(null,t);
            if(t===ERROR) { return ERROR; }
        }
        return t;
    }
}
var grandgrandma = compose(mother,father,father);
{% endhighlight %}
虽然这样 `compose` 复杂了一点儿，但毕竟是"我们不关心的实现"。 只要“库”给我们提供了一个这样的`compose` 函数，我们就可以写出简洁的代码了。

### 3. 初入单子

可是，很多时候代码要处理的不仅仅是"错误",所以一个"库"提供的不能仅仅只是这种函数复合的方式。另外，这样获得一些函数的复合函数很容易，要获得“我们想要的数据”还是需要对这个函数进行调用。当仅仅需要对这个函数进行调用一次，这样写就可能显得有点儿不太方便了:
{% highlight javascript linenos %}
var grandgrandma = compose(mother,father,father)("Peter");
{% endhighlight %}
另外，这样的代码不太方便阅读，毕竟人还是习惯从左到右读文字，即使把 `compose` 参数的顺序改变一下也不方便阅读。如果把函数复合的方式"挂在"数据上，作为对象的方法是不是更好的呢？ **当想使用某种方式复合的时候选取合适的数据类型就好了**。

单子就是选择了这种方式，单子对大量的复合方式进行了总结，把所有的复合方式总结为下图所示的形式:

<p class="block-image"><img alt="基本单子" src="/image/monad/monad-basic.png" /><br />基本单子1</p>

在图中的“变量”既表示变量又表示变量的类型，在以后的途中我也经常这么做,大多数时候这并不会引起歧义。比如`x`既表示`fn2`的参数是`x`也表示`fn2`输入参数类型是`x`。`m x`也是表示类型和变量(虽然大多数语言要求变量名不能有空格)，一种和`x`类型有关的类型，当然这里是指单子类型。另外,`x`和`y`可以代指相同类型。

很多时候`fn1`输入的类型并不是`fn2`所要求的;有些时候我们可能需要额外的处理，比如判断是否出错……不论什么处理，对使用者来说并不关心他们的实现。为了更通用，“我们不关心的实现”在函数调用之前和之后进行一定的处理，在此我分别称作“后处理”和“提取”。这两步可能很复杂，也可能不做任何的处理。另外，最后一步的“提取”大部分情况下是不需要，而是直接输出`m o`。比如在错误处理中，如果出错`m o`里面会包含错误信息，这个时候我们一般输出错误信息，也就是输出`m o`，而不是什么都不输出;如果执行正确，`m o`里面就包含要输出的结果，其实也可以输出`m o`。

为了方便使用，在上述几个函数复合的流程图中，找出并选择为一个“周期”把他作为一个叫做`bindM`。然后再做一点儿小“cheet”：定义一个`returnM`函数，这个函数什么也不做，仅仅把`input`转换为对应的单子类型，对这个变量进行“提取”之后结果就是`input`，如图所示:

<p class="block-image"><img alt="基本单子" src="/image/monad/monad-basic-2.png" /><br />基本单子2</p>

这样我们就可以方便的进行链式调用(第一个“后处理”一般也可以忽略)。`bindM`是单子类型对象的方法，例如`(m x).bindM(fn2)`返回值是`m y`，而`m y`也有一个属于自己的`bindM`方法，其返回值是`m z`。所以`main = compose(fn3,fn2,fn1)`函数的实现就是：
{% highlight javascript linenos %}
var main = input => returnM(input).bindM(fn1).bindM(fn2).bindM(fn3);
// or
var output = fn1(input).bindM(fn2).bindM(fn3);
var output = returnM(input).bindM(fn1).bindM(fn2).bindM(fn3);
{% endhighlight %}
后面两种求`output`的方式分别对应上面的两个图，后者虽然比较复杂和效率有点儿低，但是在语义上会带来一些方便。其实`returnM`的主要目的是进行类型转换，fn1~fn3这几个函数返回的是单子类型，在类型不符合的时候可以使用`returnM`进行类型转换。由于Haskell 可以根据返回值的类型进行选择合适的类型，可是 Javascript 是弱类型，这个函数没有太大的必要，只能自己注意返回值类型。

在基本的函数复合中，“后处理”和“提取”其实就是不做任何的处理。而且任何类型都可以进行基本的复合。所以可以为基本的函数复合实现一个`bindM`。

{% highlight javascript linenos %}
Object.prototype.bindM = function (fn){ 
    // (m x) === toPrimitive(this);
    // x === (m x);
    return fn(toPrimitive(this));
}
// 于是就可以
var grandmaName = "Peter".bindM(father).bindM(mother);
var grandgrandmaName = "Peter".bindM(father).bindM(father).bindM(mother);
// 整个程序可以读为"Peter的 father 的 father 的 mother"，显然就是"Peter"的曾祖母。

// add4 multi2 等函数，略
var _10_ = (1).bindM(add4).bindM(mutli2);
// (1 + 4) * 2 = 10
{% endhighlight %}
因为 Javascript 在进行方法调用的时候会把基本类型转换为对象，所以对于基本类型要用`toPrimitive`再转换回基本类型 [toprimitive 函数参考这里]({% post_url 2016-04-07-javascript-type-conversion %})，关于错误处理的复合留到后面在说。

### 4. 总结

编程就是函数复合，单子提供了一种复合的方式。单子总结了不同的函数复合方式，把他们总结为统一的形式，并且提供了方便的链式调用。似乎基本函数复合的单子平淡无奇，而且似乎有点儿麻烦。因为单子总结了各种复合方式，所以其也包含基本的函数复合。因为基本复合很简单，所以用统一的代码处理就会导致：简单的复合写起来可能比较麻烦，但是对于复杂的东西写起来也是这么简单。后面我会慢慢的介绍其他的单子。你会发现我们使用的单子的时候，代码都是类似简单的链式调用，这是因为在`bindM`中进行了复杂的工作。

[MONADINPIC]: http://adit.io/posts/2013-04-17-functors,_applicatives,_and_monads_in_pictures.html
