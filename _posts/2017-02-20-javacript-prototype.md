---
layout: post
title: "Javascript 原型链"
categories: 前端开发
tags: javascript 原型链
---

### 1. 类

在C或者Java里，`int a;`定义了一个`int`类型的变量`a`。其中`int`是类型的名字，`a`是具体的变量。

Javascript 模仿自 Java, 有一部分面向对象编程的部分。在面向对象的编程中，类就是像`int`那样是类型，对象就是像`a`那样的变量。他们的区别是:`int`是编译器预先定义好的类型，但是类是我们自定义的类型，需要自行写代码创建这个类以及这个类型的变量。比如:

{% highlight javascript linenos %}
class Point {
    constructor(x,y){ // 构造函数，解释器通过构造函数创建一个Point类型的变量
        // 其中的 this 是新创建出来的对象(变量)
        this.x=x;this.y=y; // 给新创建的对象添加一些属性
    }
    show(){ return "(" + this.x +"," + this.y + ")";}
}; // 这样就定义了一个 Point 类

var p = new Point(1,2); // 调用构造函数生成一个Point类的对象
// 或者说 Point p = new Point(1,2);
// 即定义了一Point类型的变量 p，赋值为某个值。
p.show();// "(1,2)"
p.x; // 1
{% endhighlight %}

可是 Javascript 是弱类型，写出了`Point`类型又有什么意义呢，又不能写`Point p = xx`。于是在JS里面把类型和函数合二为一。当定义了一个函数，那么这个变量名称**既是一个函数也是一个类型的名称**，并且**这个函数是这个类型的构造函数**(也是一个普通的函数)。

{% highlight javascript linenos %}
function Point(x,y) 
{ // Point类的构造函数，即上面的 constructor
    this.x = x; this.y=y;
    this.show = function() { return "(" + this.x +"," + this.y + ")";}
}
var p = new Point(1,2);
p.show();// "(1,2)"
{% endhighlight %}

这样`Point`有三个意义：1. Point类型;2. Point 是Point类的构造函数;3. Point 是一个普通的函数。这样做不会出现歧义，因为没有`Point p`这种写法，所以在`p instanceof Point`中`Point`表示一个类型,在`new Point()`中表示构造函数，其他情况下`Point`就是一个普通的函数。不过也没有太大的必要去区分`Point`是构造函数，还是普通函数。一般情况下构造函数中要给`this`添加属性。普通函数调用里对`this`的操作是不建议的。但是这样做存在一个问题:

{% highlight javascript linenos %}
var p1 = new Point(1,2);
var p2 = new Point(3,4);
p1.show === p2.show; // False
p1.show.toString();
// 'function() { return "(" + this.x +"," + this.y + ")";}'
p2.show.toString();
// 'function() { return "(" + this.x +"," + this.y + ")";}'
{% endhighlight %}

即`p1.show`和`p2.show`是不同的函数，但是他们却有着相同的代码。这样在内存中是一种浪费，尤其是类中的方法和该类创建的变量都很多的时候。(`p1.x`和`p2.x`各自占用一个内存是必要的，因为他们需要保存不同的值) 下面是一种解决方法：

{% highlight javascript linenos %}
var PointMethod = {  // 把所有方法放到一个额外的对象中
   show: function() { return "(" + this.x +"," + this.y + ")";},
   toValue: function() { return [this.x,this.y];},
   // ... other method
}
functioin Point(x,y) {
    this.__method__ = PointMethod;
    this.x = x; this.y=y;
}
var p1 = new Point(1,2);
var p2 = new Point(3,4);
//当我们要访问一个方法时
p1.__method__.show(); // 先不考虑this的问题
p1.__method__.show === p2.__method__.show; // true
{% endhighlight %}

这样有点儿麻烦，但是 Javascript 正是使用了这种方法。解释器帮我们处理了一部分工作,所以我们方便的写出和上面工作原理一样的如下代码:

{% highlight javascript linenos %}
function Point(x,y)
{   // 当使用 new Point（即作为构造函数的时候）自动添加:
    // this.__proto__ = Point.pototype;
    this.x = x; this.y = y;
}
Point.prototype = {  // PointMethod
   show: function() { return "(" + this.x +"," + this.y + ")";}
   toValue: function() { return [this.x,this.y];}
}
var p1 = new Point(1,2);
var p2 = new Point(3,4);
p1.show(); 
// 其实是 `p1.show`、`p1.__proto__.show`、`p1.__proto__.__proto__.show` ... 中找到的第一个
p1.show == p2.show; // true;
{% endhighlight %}

在 javascript 里面有两个特殊的类:`Object`,`Function`。所有对象都是`Object`类的，所有函数都是`Function`类的，再加上函数也是对象，于是他们之间有着微妙的关系。

{% highlight javascript linenos %}
var obj = {}; // 等价于 var obj = new Object();
obj instanceof Object; // true
obj.__proto__ === Object.prototype; // true;

// 函数 Object 是否是 Function 类的？
Object instanceof Function; // true
Object.__proto__ === Function.prototype; // true

// 函数 Function 是否是 Function 类的？
// 上面说过函数名和类型名称可以(或是故意让他们)重名
Function instanceof Function; // Function
Function.__proto__ === Function.prototype; // true

// 函数 Function 是否是 Object 类的？
Function instanceof Object; // true
Function.__proto__ === Object.prototype; // false! Why? 看后面
{% endhighlight %}

### 2. 继承

面向对象的语言还有一个精髓是继承。

{% highlight javascript linenos %}
// NamedPoint 继承 Point 
class NamedPoint extends Point {
    constructor(name,x,y){
        super(x,y); // 调用 Point 类的构造函数
        this.name = name;
    }
    getName() { return this.name; }
}
var np = new NamedPoint("original",0,0);
var p = new Point(1,2);
np.show(); // "(0,0)"
np.show === p; // true
{% endhighlight %}

其实要实现这样的方案也是不是很难，用之前面说过的知识就可以了。前面说过，当读取`np.show`的时候其实解释器读取的是以`np.show`, `np.__proto__.show`, `np.__proto__.__proto__.show` ...的顺序找的的第一个值。在`NamedPoint`里面没有`show` 方法，即`np.show`, `np.__proto__.show`都是不存在的。那么我们让`np.__proto__.__proto__` 等于 `Point.prototype` 那么`np.show`读取的就是`Point`中定义的方法`show`了！即`np.__proto__`(`NamedPoint.prototype`) 是`Point`类的变量。解决方案就现而易见了：

{% highlight javascript linenos %}
function NamePoint(name,x,y){
    Point.call(this,x,y); // super(x,y); 调用父类构造函数
    this.name = name;
}
// prototype 是什么类型的 NamedPoint 就继承了哪个类
NamePoint.prototype = new Point(0,0);
// delete Point.prototype.x;
// delete Point.prototype.y;
Point.prototype.getName = function() { 
    return this.name;
}
var np = new NamedPoint("original",0,0);
var p = new Point(1,2);
np.show(); // "(0,0)"
np.show === p; // true
{% endhighlight %}

虽然可以，但是比较麻烦，已经是javascript(ES5.1)里面最好的解决方案了。那么`Point`类继承了什么类？在上面的代码中，无论是默认的还是自定义的`Point.prototype`都是`Object`类。所以`Point`继承了`Object`类。这就是说`np.__proto__.__proto__.__proto__`是`Object.prototype`。所以：
{% highlight javascript linenos %}
p.toString === Object.prototype.toString; // true
p.toString(); // "[object Object]"
{% endhighlight %}

<p style="text-align:center;text-indent:0">
    <img src="{{ "/image/javascript/point-prototype.png" | prepend:site.baseurl }} "/><br />
<code>Point</code>类继承了<code>Object</code>类，所以<code>p</code>可以通过上述方式访问<code>Object</code>类的方法<code>toString</code>
<hr />
</p>
可见，子类继承了父类的所有方法，所以如果一个地方可以写下`p.show()`那么`np.show()`同样是可以的。所以一个父类变量出现的地方，出现子类是没有问题的(当然结果可能不是你想要的)。如果把 `np` 当作`Point` 类也是没有问题的，只是这样用浪费了部分内存。`np instanceof Point` 可以返回 `true`。于是，`instanceof` 就是检查`Point.prototype`是否在`np`的原型链(`__proto__`形成的链表)里面了。

任何对象通过递归的访问`__proto__`，最终的结果都是`Object.prototype`，所以说，所有的对象都继承了`Object`类的方法，所有对象都是`Object`类的，`Object`类是所有类的父类。仔细研究一下下图还是大有裨益的：

<p style="text-align:center;text-indent:0">
    <img src="{{ "/image/javascript/chrome-prototype.png" | prepend:site.baseurl }} "/>
</p>

`Object`,`Function`,`Point`都是类名即也是函数，所以都是`Function`类的对象。`p`是`Point`类的对象，`Function.prototype` 和`Point.prototype`  都是`Object`类的对象，所以他们的`__proto__`分别是什么已经很明确了。

### 3. 重载

`NamedPoint.prototype.show` 是 `NamedPoint.prototype.__proto__.show`，即`Point.prototype.show`。这仅仅是在读取的时候是这样的，但是写入的时候就不是这样了，写入仅仅会在`NamedPoint.prototype`中添加/改变这个属性，而不会去原型链中去修改这个属性。
{% highlight javascript linenos %}
//写入时 Namepoint.prototype.show 不存在就创建新的，而不会去原型链中去查找
NamedPoint.prototype.show = function(){
    return this.name + ":(" + this.x + "," + this.y + ")";
}
np = new NamedPoint("Original",0,0);
np.show(); // "Original:(0,0)"
var p = new Point(1,2);
p.show(); // "(1,2)"
p.show === np.show(); // false
{% endhighlight %}

这样可以实现类中方法的重载，即`NamedPoint` 继承了`Point`中的方法，同时也重新定义了`show`方法。当然也可以给每个对象重载新的方法。   

<p style="text-align:center;text-indent:0">
    <img src="{{ "/image/javascript/namedpoint-prototype.png" | prepend:site.baseurl }} "/>
</p>

### 4. 其他

`Point.prototype` 可以被赋值为基本类型。此时执行，`var p = new Point()`,`p.__proto__`会被赋值为`Object.prototype`

不建议直接操作一个对象的`__proto__`属性，虽然在某些nodejs的代码里经常见到。

`Point.prototype` 可以不只是函数(方法)，也可以是变量。只是如果只能从原型链上读取的话，那么就是一个常量。而一个类的常量一般作为类的一个属性，比如`Number.MAX_VALUE`。如果当作类内的静态变量，为什么不用闭包呢？

把所有的`Point`类型变量替换为`NamedPoint`是语法上不会出错，但逻辑上结果是否相同要靠自己保证。但是有一种设计模式叫做"里氏替换原则",就是说要保证这种替换不会改变程序的运行结果。

`P.__proto__ = Point.prototype` 是在`p = new Point()`的时候发生的:
{% highlight javascript linenos %}
function Point() {}
Point.prototype = { show:function(){return "1";} }
var p = new Point();
p.show(); // 1
Point.prototype.show = function() { return "2"; }
p.show(); // 2
Point.prototype = { show:function(){return "3";} }
p.show(); // 2  思考一下为什么
{% endhighlight %}

由于 `Point` 既是一般函数，又是构造函数，区别就是有没有`new`，可人总是要犯错误的，有些时候想把其当作构造函数却忘记写`new`就不好了。下面是一种还可以的解决方法，虽然不完美,但是足够了。
{% highlight javascript linenos %}
function Point(x,y)
{
    if(!this instanceof Point) {
        // 如果当作普通函数用
        return new Point(x,y);
    }
    // 当作构造函数用
    this.x=x; this.y=y;
}
{% endhighlight %}

一个方便继承的函数,随着ES2015的到来，可能用处不大了，仅供参考
{% highlight javascript linenos %}
var hasOwn = Object.hasOwnProperty;
function extendClass(child,parent) {
    var key;
    for( key in parent){
        if(hasOwn.call(parent,key)){
            child[key] = parent[key];
        }
    }
    function Ctor(){this.constructor = child;}
    Ctor.prototype = parent.prototype;
    child.prototype = new Ctor();
    child.SUPER = parent.prototype;
}
function Point(){}
function NamedPoint(){}
extendClass(NamedPoint,Point);
Namedpoint.prototype.show = function() {};
// ....
{% endhighlight %}


