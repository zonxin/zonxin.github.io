---
layout: post
title: "我的Monad教程-2:错误处理"
categories: 前端开发
tags: javascript 单子 Monad 错误处理
---


### 2. 错误处理

这种"模式"主要用于错误处理以及顺序执行。或者说后面一步能否执行取决于前一步执行
是否能正确执行。就是上面`compose`类似的函数复合方式.比如，链接数据库成功才能查
询数据，查询成功才能发送数据给用户，如果其中任意一步出错则整个操作就失败了。

#### 2.1. Maybe
简单一段"我们不关心的实现":
{% highlight javascript linenos %}
function Maybe(value) { this._value = value;}
Maybe.prototype = {
    constructor:Maybe,
    getMaybe: function() { return this._value;},
    // 注意这个函数
    bindM: function(fn) {
        if(this !== Nothing ) { return fn(this.getMaybe()); }
        return Nothing;
    },
    isJust: function() { return this !== Nothing;},
    isNothing: function() { return this === Nothing;}
}
var Nothing = new Maybe();  // 约定此变量表示错误
function Just(x) { return new Maybe(x);}
{% endhighlight %}
其中`Nothing`表示处理出错,既然选择这个“库”提供的错误处理方式，就必须遵循这个规
定，`Just`中保存了处理正确情况下的结果。这样做相当于在原始的字符串所有可能取值
上额外添加了一个取值`Nothing`，这样就不需要用特殊值来表示错误。原本代码使用空字
符串来表示处理出错，但是谁能保证将来空字符串不可以表示一个合法的名字呢。改写一
下`father` `mother`函数:
{% highlight javascript linenos %}
function father(name) 
{
    if(Math.random() > 0.8) { 
        return Nothing; // 处理出错
    }
    return Just(name + "'s father");
}
function mother(name) 
{
    if(Math.random() > 0.8) { 
        return Nothing; // 处理出错
    }
    return Just(name + "'s mother");
}
{% endhighlight %}
于是要获得一个人的曾祖母就可以写为:
{% highlight javascript linenos %}
var grandgrandma = father("Peter")
    .bindM(father)
    .bindM(mother);
    // 或者
var grandgrandma = Just("Peter") // 不严谨的说，这句表明我们使用Maybe提供的错误处理方式
    .bindM(father)
    .bindM(father)
    .bindM(mother);
{% endhighlight %}
于是整个程序可以读为"Peter的 father 的 father 的 mother"，显然就是"Peter"的曾祖母。

从另一个角度看，`mother` 函数仅仅是整个获取曾祖母函数中的一小部分, 当编写
`mother`函数的时候"我们关心的数据"就是上一步执行正确时候的执行结果。可以使用这样的方式获得：
{% highlight javascript linenos %}
Just("Peter").bindM(father).bindM(father)
    .bindM( function(x) { 
        // 理解为 var x = Just("Peter").bindM(father).bindM(father)
        // x 就是这一小步中要处理的数据
    });
{% endhighlight %}
通过上面"我们不关心的实现"中的 `bindM` 函数，可以知道匿名函数就是 `bindM` 的形
参`fn`，而 `bindM` 给函数提供的参数就是 `Just` 中的值(上一步处理正确情况下的返
回值为"Peter"的祖父),即 x 就是 "Peter"的祖父,是在这一小步中"我们关心的数据",
**是前面所有步骤都执行正确情况下，这一步需要处理的数据**。不过在此函数中只需要知
道`x`是某个人，此函数是要完成查询一个人母亲的任务就好了，而不需要关心其他部分是
如何实现的，它只需要做好查询一个人母亲的任务就好了。再写个例子，比如我们要过滤
和处理用户的输入:
{% highlight javascript linenos %}
var str = "user input";
var Output = Just(str)
    .bindM(x=> x.length > 0 ? Just(x):Nothing) // 长度大于 0
    .bindM(y=>/\n/.exec(y) ? Nothing:Just(y)) // 不能有换行
    .bindM(z=>Just(z.replace(/\t/g,"  "))) // Tab替换为空格
{% endhighlight %}
通过 `bindM` 的实现可以知道，如果上述步骤任意一步返回`Nothing`,则最终结果为
`Nothing`。只有每一步成功整个操作才会成功，这正是"我们不关心的实现"，上述的"x"
"y" "z" 就是每一小步要处理的数据，他们都可以认为是用户的输入，每一小步做好自己的过滤工作就好了。
上述代码也可以写为:
{% highlight javascript linenos %}
var isNotEmpty = x => x.length > 0 ? Just(x):Nothing);
var isOneLine  = y=>/\n/.exec(y) ? Nothing:Just(y));
var replaceTab = z=>Just(z.replace(/\t/g,"  ")));
var str = "user input";
var Output = isNotEmpty(str)
    .bindM(isOneLine)
    .bindM(replacetab);
if(Output.isNothing()) { console.log("输入不符合要求"); }
// 又出现了讨厌的 if，其实可以消除
{% endhighlight %}
这样减小了函数粒度，提高了函数的复用性和可读性，而且多个函数的复合也是很简单的。

#### 2.2. Either

当使用 `Maybe`的时候最终我们只知道出错了，但并不知道错误的原因。
或许"我们不关心的实现"可以更复杂一点儿，用来处理错误信息。
{% highlight javascript linenos %}
function Either(value) { this._value = value;}
Either.prototype = {
    constructor:Either,
    getEither: function() { return this._value;},
    bindM: function(fn) {
        if(this.isRight()) { return fn(this.getEither()); }
        return this;
    },
    isLeft: function() { return this._lr_ === "left";},
    isRight: function() { return this._lr_ === "right";}
}
function Left(value) { var e = new Either(value);e._lr_="left"; return e;}
function Right(value) { var e = new Either(value);e._lr_="right";  return e;}
{% endhighlight %}
其中`Left`中保存错误信息，`Right`中保存了处理正确情况下的结果。改写一下`father` `mother`函数为
{% highlight javascript linenos %}
function father(name) 
{
    if(Math.random() > 0.8) { 
        return Left("Can not find " + name + "'s father");
    }
    return Right(name + "'s father");
}
function mother(name) 
{
    if(Math.random() > 0.8) { 
        return Left("Can not find " + name + "'s mother");
    }
    return Right(name + "'s mother");
}
// 注意，下面的代码是不变的
var grandgrandmaName = Just("Peter")
    .bindM(father)
    .bindM(father)
    .bindM(mother);
{% endhighlight %}
上述代码和`Maybe`相似:`Left`相当于`Nothing`，只是其中可以保存错误信息，`Right`
相当于`Just`。在此就不做过多的阐述了。既然有了错误信息，给"我们不关心的实现"加
个`catch`方便我们错误处理，当然`Maybe`也可以加。
{% highlight javascript linenos %}
Either.prototype.catch = function(fn){
    if(this.isLeft()) { return fn(this.getEither()); }
    return this.getEither();
}
var grandgradma = father("Peter") 
    .bindM(father)
    .bindM(mother)
    .catch(err=> {console.log(err);return "";});
    // 这里的错误处理仅仅是回归到以前假设空表示出错。
{% endhighlight %}
上述代码还是存在问题，就是不支持异步操作，可是做为 Javascript 不支持异步怎么行呢？

#### 2.3. Promise

在上述 Either 的实现中会有三个全局变量`Either`,`Left`,`Right`，这点我们不太喜欢
。可是我们可以通过再复杂一点儿的实现只留下一个全局变量，如:
{% highlight javascript linenos %}
var Promise = (function(){
    // 原始 Either 实现
    function Either(value) { this._value = value;}
    Either.prototype = {
        constructor:Either,
        getEither: function() { return this._value;},
        bindM: function(fn) {
            if(this.isRight()) { return fn(this.getEither()); }
            return this;
        },
        isLeft: function() { return this._lr_ === "left";},
        isRight: function() { return this._lr_ === "right";}
    }
    function Left(value) { var e = new Either(value);e._lr_="left"; return e;}
    function Right(value) { var e = new Either(value);e._lr_="right";  return e;}
    
    function Promise(fn){
        // 这样，在使用的时候可以不写 return 
        var ret;
        resolve = value => { ret = Right(value); };
        reject = value =>  { ret = Left(value); };
        fn(resolve,reject);
        return ret;
    }
    return Promise;
})();
 // 于是`father`函数就可以写为:
function father(name)
{
    // to do 链接数据库等
    return Promise( function(resolve,reject){
        if(Math.random() > 0.8) { 
            // 通过上面对 Promise 的定义，这里可以不用写 return
            reject("Can not find " + name + "'s father"); // 不需要再写 return;
        }
        resolve(name + "'s father"); // 不需要再写 return;
    });
}
{% endhighlight %}
这样我们的代码就不受制于`Right`和`Left` 这两个变量名字。ES2015 就提供了一个这样
的 `Promise` 对象，只是它的实现比上述代码更复杂，而且支持异步操作(准确来说是异步操作)。不过既然是"我们不关
心的实现" 就不写实现了。不过呢，在 ES2015 中，方法`bindM`叫做`then`,也支持
`catch`。写个 AJAX 的例子:
{% highlight javascript linenos %}
var $ = jQuery;
function getJSON(url){
    return Promise(resolve,reject){
        $.ajax({ url:url,
            success:function(result){ 
                resolve(result); 
            },
            error:function() { reject("Error!"); }
        });
    }
}
getJSON("http://example.com/path/to/JSON")
    .then(info=>console.log(info))
    .catch(err => console.error(err));
{% endhighlight %}
使用方法和 `Either` 区别不大，只不过`Either`不支持异步操作。另外，Promise 中的 `then` 是
支持两个参数的，至于什么意义，自行搜索。

像这样，定义了类似 `bindM` 函数的数据类型(其实还有一个叫return的函数)，叫做单子Monad。
不过这些函数是要满足某些性质，至于是什么样的性质，后面再做介绍。而要求的这些性质比较少，于是一大批数据类型都可以定义出
符合要求的`bindM`,所以很多东西都是单子，只是这些 `bindM` 就看上去毫不相干。不过，他们都有一个通用的
目的，方便函数复合和调用，不同的 `bindM` 的实现和定义了不同的复合方式。比如如果我们要进行有错误处理的复合
我们可以选择`Maybe`或是`Either`。

### 3. 数组遍历

数组也可以定义一个符合要求的 `bindM` 函数，而这个函数的作用是完成类似数组遍历的作用[注]。
{% highlight javascript linenos %}
Array.prototype.bindM = function (fn){
    var len = this.length, result = [], i,ret;
    for(i = 0; i < len; i++){
        ret = fn(this[i]);
        // ASSERT(ret instanceof Array);
        result = result.concat(ret);
    }
    return result;
}
{% endhighlight %}
于是遍历数组就可以:
{% highlight javascript linenos %}
[1,2,3].bindM( x=> console.log(x));

[1,2,3].bindM(x => {
    return [x+10,x+100]; // 此处 x 取值分别为 1,2,3
}).bindM(y =>{
    return [y*10]; // 为了保持类型的统一，此处最好是返回一个数组
});
// [110,1010,120,1020,130,1030]

[1,2,3].bindM(x => {
    return [100,200,300].bindM( y => {
        // 这样写在这个函数里面就可以访问上一层的变量 x
       return [x+y]; 
}})); 
//[101,201,301,102,202,203,301,302,303]
{% endhighlight %}
在第二段代码中 x 遍历 [1,2,3] 中的值, y遍历 [100,200,300] 中的值，返回值是着两个值的和
[x+y]，因此最终会返回含有九个值的数组。从`bindM` 的实现来看，一个`bindM` 是一个`for`循环，
两层 `bindM` 就是两层 `for` 循环，所以计算结果含有九个值。

#### do语法糖

第二段代码似乎写起来有点儿复杂，但是结构性很强。于是在 Haskell 中发明了一个 do
语法糖(可以理解为一种简写的方式或是宏)。上述代码可以简写为:
{% highlight haskell linenos %}
do
    x <- [1,2,3]
    y <- [100,200,300]
    [x+y]
{% endhighlight %}
是不是看起来更清晰，把`<-` 看作赋值，是不是更清晰一点儿，其意义就是 x 赋值为后面数组的每一个元素。

由于是仅仅类似于字符串替换的东西(隐藏bindM函数的调用嗯和匿名函数的创建)，Maybe 和
Either 也可以用 do 语法糖。do 语法糖可以极大的简化代码，由于许多东西都可以归结
为单子，定义`bindM` 函数，所以这样的代码出现率也特别高，写出来的程序可以很短。
下面完成一个查询一个人的祖父母，外祖父母的函数(假设用Maybe),有点儿复杂，但是仔细看看还是可以看出来的:
{% highlight javascript linenos %}
var grandprants = Just("Peter").bindM( p =>{ // var p = "Peter"
    father(p).bindM(dad =>   //  var dad = father(p);
        mother(p).bindM( mon =>  // var mon = mother(p);
            father(dad).bindM(g1 => // var g1 = father(dad);
                 mother(dad).bindM(g2 => 
                     father(mon).bindM(g3 => 
                         mother(mon).bindM(g4 => 
                             Just([g1,g2,g2,g4]);
); ); ); ); ); ); );
{% endhighlight %}
用 do 就方便阅读多了，大家肯都见过类似的代码:
{% highlight haskell linenos %}
grandprants = do
    p <- Just("Peter");
    dad <- father(p);
    mon <- mother(p);
    g1 <- father(dad);
    g2 <- mother(dad);
    g3 <- father(mon);
    g4 <- mother(mon);
    Just([g1,g2,g2,g4]);
{% endhighlight %}
这里面`<-` 的意义就是"赋值"呗，只是它可以自动进行错误处理。

#### 数组函数的复合方式

上面说过，单子其实定义了一种函数复合的方式，当我们想使用不同的复合方式选择合适的数据类型就可以了。
那么在数组里的这个`bindM` 定义了怎样的复合方式呢？下面以一个例子来说明，一个简单的问题:
在一个 8x8 的棋盘上，象棋中的马经过三步从一个点到达哪些点。

{% highlight javascript linenos %}
// 定义一个坐标
function Point(x,y) { this.x = x; this.y = y; }
Point.prototype = {
    constructor:Point,
    equal: function(p) { return this.x===p.x && this.y===p.y; },
    toString: function(p){ return "(" + this.x + "," + this.y + ")"; }
}
// 判断是否合法
function isValidPos(pos) {
    return pos.x >= 1 && pos.x <= 8 && pos.y >= 1 && pos.y <= 8;
}
var Unit = {};
function guard(b) { return b?[Unit]:[]; }
// 移动一步
function moveKnight(pos)
{
    var x = pos.x,y = pos.y;
    // 使用 doM 和 eval 模拟 do 糖
    // doM 的具体代码参见 https://github.com/zonxin/jHaskell/blob/master/src/doM.js
    var code = doM(function(){
            next <- [new Point(x+2,y-1),new Point(x+2,y+1),new Point(x-2,y-1),new Point(x-2,y+1),
                     new Point(x+1,y-2),new Point(x+1,y+2),new Point(x-1,y-2),new Point(x-1,y+2)];
            unNeededVar <- guard(isValidPos(next)); // [注]
            [next]; 
        },"");
    // console.log(code);
    return eval(code);
}
function reachIn3(from)
{
    // 简单来说，移动三步，就是三个移动一步函数的复合
    return [from].bindM(moveKnight)
        .bindM(moveKnight)
        .bindM(moveKnight);
}
console.log("===========================================================");
var start = new Point(6,2);
console.log(reachIn3(start));
{% endhighlight %}
此段代码中最不容易理解的估计就是 `guard` 函数了，这个函数仅仅是根据传入的参数返
回一个空数组，或是含有一个元素的数组。通过上面对于 do 的介绍，我们知道`moveKnight` 
里面的 doM 其实是两层的循环。对于第一层循环，循环的次数是 8, `next`遍历后面数组中的没一个值,第二层会根据 guard 返回值的不同循环1次或是0次。
其实，这相当于一个`if`。即会有类似如下的代码，虽然看起来有点儿奇怪，但是还是很容易看懂的。
{% highlight javascript linenos %}
// var nextPoses = [...];
var result=[];
for(var i=0; i < nextPoses.length;i++){
    var next = nextPoses[i]; // next 遍历所有的值
    // 下面几句相当于 if(isValidPos(next))
    var t = guard(isValidPos(next));
    for(var j=0;j<t.length; j++) { // 循环 0 次或是 1 次
        var unNeededVar = t[j];
        result = result.concat([next]); //  把 next 放入结果
    }
}
{% endhighlight %}
即这段代码的意思是，遍历所有可能的下一步的位置，检查这些位置是否合法，只过滤合法的结果。所以那个函数叫做`guard`(门卫)。

数组提供的单子就是把前面处理得到的多个结果（数组），在分别用后面的函数处理，最
后把所有结果放到一起。马移动一步可以移动到多个可能的位置，后面在移动一步就是遍
历这些所有可能的位置，获得的结果就是移动两步可能到达的位置。所以，`reachin3` 其
实列出了所有马移动三步所能到达的位置(不过没有除去重复的)。

这就是数组定义的单子提供的复合方式。

### 4. 管道(pipe)

#### 写一个简单的 gulp 模块

### 5. 状态单子



[MONADINPIC]: http://adit.io/posts/2013-04-17-functors,_applicatives,_and_monads_in_pictures.html
