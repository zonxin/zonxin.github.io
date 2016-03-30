---
layout: post
title: "Javascript中this的值"
categories: 前端开发
tags: javascript this
---

在 javascript 中，任何函数中都可以出现`this`，但是它的值是在运行时决定的。说简单一点儿就是`this`的值取决与你如何调用这个函数，而不是函数定义所在的位置。另外在函数调用的时候可以使用`apply/call`指定`this`这样就实现了代码的复用。大多数javascript 的内置函数设计的尽量通用。

### 1.一般函数调用

在一个普通的函数中，`this`的值在浏览器中是`window`在nodejs中是`global`（后面不做区分，假设在浏览器中执行）。在最外层的作用域中`this`为`window`。例如

{% highlight javascript linenos %}
console.log(this); //输出 window
func(); //输出为 window

function func()
{
    console.info(this); 
}
{% endhighlight %}

可是这样存在一个问题，如果一个函数是构造函数，一般在这个函数中是会给`this`添加属性的。谁也能不能保证不犯错误，有时候当我们想把函数作为构造函数的时候，可是却写成了一般函数调用的形式，这个时候这一段代码就是给`window`添加属性，此时程序不会报错，但是明明又不是我们想要的（javascript的坑不止这一个）。所以为了让 javascript 变得更好，就提出了一种严格模式，在某一块代码的一开始写一句`"use strict"`，对于不支持的浏览器来说这个只是一个字符串。但是对于支持严格模式的浏览器来说，会进入严格模式。在严格模式下，一般的函数调用中 `this` 的值是 `undefined`。

{% highlight javascript linenos=table %}
func(); //输出为 undefined

function func()
{
    "use strict"
    console.info(this); 
}
{% endhighlight %}

### 2.作为对象的方法

如果函数作为对象的方法，也就是说调用方法是`对象.方法()`，那么这个时候所执行的函数中`this`就是这个对象的引用。

{% highlight javascript linenos=table %}
var obj={};
function func(){ console.info(this) }
obj.f = func;
obj.f(); //输出 obj
func(); //输出 window
{% endhighlight %}

尝试一下代码的输出：

{% highlight javascript linenos=table %}
function func {console.info(this)};
var obj={};
obj.f=func;

obj.f();    // obj
(obj.f)();  // obj
(a=obj.f)(); // window
(0,obj.f)(); //  window
{% endhighlight %}

前两个是等价的（`.` 的优先级比函数调用高）。其实，`.`运算符的返回值不是`obj.f`中存储的值，而是一个包含`obj.f`信息的“对象”(这个只是 javascript内部实现时使用的，用 javascript 代码并不可访问)。在ECMAScript 5.1 标准中对这个返回值的描述是：

> Retrun a value of type Reference whose base value is **baseValue** and whose referenced name is **propertypname**,
and whose strict mode flag is **strict**.

本例中 **baseValue** 为`obj`， **propertypname**是字符串类型的属性名`"f"`，后面那个**strict**是是否处于严格模式的标记。所以说这段话的意思就是`obj.f`的返回值是一个 **baseValue** 为`obj`, **propertypname** 为`"f"`的“对象”。而`this`的值其实是取上述返回值的 **baseValue** ，因此，在函数中`this`是`obj`。对于第三个，首先`a=obj.f`是一个赋值表达式，虽然 `obj.f`的返回值是一个包含`obj.f`信息的“对象”，但是赋值表达式的返回值是`=`右边表达式的返回值“求值”(`GetValue`)，把“对象”中的值取出来，所以`a=obj.f`的返回值就是一个函数。然后再对这个函数进行调用调用，显然这个返回值的 **baseValue** 是没有定义的。而函数调用初始话作用域的时候，如果`this`为空值，那么就会让`this`等于`window`（非严格模式）。一般函数调用`this`是`window`的原因也是如此-- _baseValue_ 为空。最后一个与第三个相同，只不过是赋值表达式换成了逗号表达式。

{% highlight javascript linenos %}
function fn() { console.info(this); }
function fnstrict() { 
    'use strict'
    console.info(this); 
}
fnstrict.call(undefined); // undefined
fn.call(undefined); // window
fn.call(null);      // window
{% endhighlight %}

### 3. 构造函数(关键字new)

在new表达式中的 `this` 反而简单，统一规定为一个新创建的以这个函数为原型的对象。

{%highlight javascript linenos=table%}
function fn(){ console.info(this instanceof fn) };
var obj = new fn(); //输出 true
{%endhighlight%}

顺便说一下，new表达式的返回值：如果这个函数返回一个对象，那么new表达式的返回值是这个对象，否则返回值是新创建的这个对象。

### 4. 调用时指定this

也很简单，在调用函数时可以显示的指明用哪个值作为`this`，所以这仅仅是一个语法问题。javascript给出的方式就是`apply/call`这两个函数只是在参数的形式上有些区别(这个区别不是本文的主要内容在此不讨论)。`call`函数的语法是`func.call(this的值，可选参数1,...)`，其中可选参数会传递给`func`。

{%highlight javascript linenos=table%}
function fn(arg){ console.info(this) };
fn.call("thisvalue"); 
// 输出 “thisvalue”, 实际为 toObject("thisvalue"),把这个字符串转换成对象
{%endhighlight%}

这样做的好处就是可以实现代码复用，javascript 中的内置函数后设计的尽可能通用，当一个对象满足某些条件的时候就可以用作为这个函数的`this`。比如一个对象只要有`length`属性就可以作为`Array.prototype`中的函数的this，参考 [《Array.prototype中函数的定义》]({% post_url 2015-07-05-javascript-array-prototype %})

### 5. 上述说的都不对

还有一种特殊情况，javascript可以指定一个固定的值作为`this`，无论如何调用这个函数`this`的值不变。产生这种函数的方式就是调用一个普通函数的`bind`方法，`bind`不仅可以指定`this`还可以指定函数调用的参数，其语法是`func.bind(this的值,可选参数1,....)`，其中可选参数是要绑定的参数，其余参数在调用的时候再给出。

{%highlight javascript linenos=table%}
function func() { console.info(this) }
bfunc = func.bind("bind this");
bfunc(); // bind this
// 原函数还是按照前面的方式确定 this
func();  // window
{%endhighlight%}

一种常用，但是代码怎么写都有点儿乱的绑定(ES2015可以解决)，代码功能不用关心，只要知道如果`this`没有正确的值`this.doSomething`调用就会失败就可以了：

{%highlight javascript linenos=table%}
var PageHandler = { 
    id: "123456", 
    init: function() {
        document.addEventListener("click", 
            (function(event) {
                this.doSomething(event.type); }
            ).bind(this), false); 
    }, 
    doSomething:function(type) { 
        console.log("Handling " + type  + " for " + this.id); 
    } 
}; 
PageHandler.init();
{%endhighlight%}

问题是上述代码为什么可以正常工作：因为`bind`后面的`this`是在init中的，而init的调用是`PageHandler.init()`。故这个this是PageHandler，所以事件监听函数绑定的this就是PageHandler。

### 6. DOM事件回调函数

作为浏览器中的javascript中还有一种函数调用，那就是事件回调函数。在事件回调函数中`this` 的值是当前触发该事件的DOM对象(部分浏览器只有使用`addEventListener`添加的事件才遵循此规定,说的就是IE)。
{% highlight javascript linenos=table %}
function evtHandle(e)
{
    console.log(this === e.currentTarget); // 总为true
    console.log(this === e.target);        // 只有在目标阶段为true
}
var elements = doucument.getElementsByTagName('*');
for(var i=0;i<elements.length; i++){
    elements[i].addEventListener('click',evtHandle,false);
}
{% endhighlight %}

#### 事件内联代码

DOM元素中添加的事件内联代码中的`this`就是当前的这个DOM元素。
{% highlight html linenos=table %}
<button onclick="console.log(this)">
    show this
</button>
{% endhighlight %}
上面代码中的`this`是它所在的DOM元素(button)。但是需要注意的是只有最外层作用域的`this`是所在的DOM元素，内层作用域代码中`this`的确定同前面几条。还是那句话，函数中`this`的值主要看函数是怎么被调用的，函数(内层作用域代码)是如何调用的是我们自己的代码写的，所以我们可以确定`this`的值，只是初始（最外层）的作用域中的`this`我们不知道，需要统一规定。
{% highlight html linenos=table %}
<button onclick="console.log((function (){return this;})());">
    show inner this
</button>
{% endhighlight %}
上述代码中的匿名函数就是一次普通的函数调用，所以`this`的值是`window`(非严格模式下)。

#### 万恶的IE

在IE下，用 `attachEvent` 添加的事件处理函数其中的`this`规定为 `window`：

{% highlight javascript linenos%}
// IE 下运行
var el = document.getElementById("id");
el.attachEvent('onclick',function(){ 
    console.info(this);   // window
}); 
{% endhighlight %}
### 作为总结

在 javascript 中，`this`的值取决与你如何调用这个函数，而不是函数定义所在的位置。函数的调用分为6种情况，也就是`this`的来源有6种：

1. 普通函数调用，`this`为全局对象或是`undefined`
1. 作为对象的方法，`this`为那个对象
1. new 表达式，`this`为以该函数为原型的新创建的对象
1. 使用 `apply`/`call`指定 `this`
1. 用`bind`绑定固定的`this`
1. 事件处理函数中的`this`是当前的触发事件的DOM元素(event.currentTarget)   
   IE attachEvent添加的事件处理函数中`this`为`window`

最后提一个问题
下面代码输出什么？
{%highlight javascript linenos=table%}
obj = { go: function() { console.info(this) } };
(0 || obj.go)() 
{%endhighlight%}

#### 参考：

1. [ES5.1 属性运算符](http://www.ecma-international.org/ecma-262/5.1/#sec-11.2.1)
2. [ES5.1 函数调用](http://www.ecma-international.org/ecma-262/5.1/#sec-11.2.3)
3. <http://javascript.info/tutorial/this>
4. <https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this>

