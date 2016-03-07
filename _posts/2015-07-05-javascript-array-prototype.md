---
layout: post
title: "Array.prototype中函数定义"
categories: 前端开发 编程语言
tags: javascript Array prototype
---

在Ecmascript标准中我们常常看到这样的描述：

>某某函数被有意设计成通用的；它的this值并非必须是数组对象。因此，它可以作为方法转移到其他类型的对象中。一个宿主对象是否可以正确应用这个splice函数是依赖于实现的。

在javascript中有两个很有用的函数`call`和`apply`，他们的作用都是使用另一个对象替换当前的对象，只是在调用的参数上有少许的区别。说的更具体一点儿就是改变`this`指向的对象。这个功能还是还有用的，可以使用一些内置的函数来处理我们自己定义的对象（好处是效率高，而且不用自己写代码）。比如删除obj中前五个和后五个数据可以这样写`[].slice.call(obj,5,-5)`。

我们比较感兴趣的是的当我们有一个对象，那些我们需要的功能可是使用内置的这些函数来处理以及对这个对象的要求是什么。我们需要知道这些内置对象的实现。这个就需要我们去查阅Ecmascript协议标准，可是大家都懂得，标准写的还是很有技术水平的，不是一时半会儿可以看得懂的。所以我就用大家熟悉的javascript近似的实现了`Array.prototype`中的函数，供大家查阅。结果发现，其实一个对象基本是只要有length属性就可以使用这个对象作为`Array.prototype`中函数的this执行而不出错，而且length属性不必非要是数字。只要可以转换成整型就可以。另外让大家知道一个函数的具体作用没有比贴出源码更好的方式了（虽然大家都不喜欢看），因此这个也可以作为一个学习javascript的资料。最后，有些函数在低版本的浏览器中没有实现，因此可以用这些代码扩展低版本的浏览器。难免有所疏漏，请大家予以指正。

{% highlight javascript linenos=table %}
Array.prototype.constructor = Array;
Array.prototype.toString = function(){
    //需要的属性 join
    var array = this; // toObject 后面这样的注释都表示强制类型转换
    var func = array.join;
    // if (! func instanceof Function) {throw "type Error"}
    return func.call(array);
};
Array.prototype.concat = function (item1)
{
    var O = this;  // toObject
    var A = []; var n = 0;
    var items = [O];var i;
    for(i = 0; i < arguments.length; i++) {
        items.push(arguments[i]); // 标准里没有使用push实现这个
    }
    while(items.length){
        var E = items.shift();  //标准里写的不是shift函数实现这个
        if(E instanceof Array){
            var k = 0; var len = E.length;
            while(k < len){
                if(E.hasOwnPropery(k.toString())){
                    A[n] = E[k];
                }
                n++; k++;
            }
        }else{
            A[n] = E;
            n++;
        }
    }
    return A;
};
Array.prototype.join = function(sepatator)
{
    var O = this;  // toObject
    var lenVal = O.length;
    var len = lenVal; //toUint32
    sepatator = sepatator || ",";
    var sep = sepatator.toString();
    if(len === 0) { return ""; } 
    var element0 = O[0];
    var R = element0 === undefined || element0 === null ? "" : element0.toString();
    var k = 1;
    while(k < len){
        var S = R + sep;
        element = O[k];
        var next = element === undefined || element === null ? "" : element.toString();
        R = S + next;
        k++;
    }
    return R;
};
Array.prototype.pop = function (){
    var O = this; // toObject
    var lenVal = O.length;
    var len = lenVal; // 强制类型转换toUint32;
    if(len === 0){
        O.length = 0;
        return undefined;
    }
    var element = O[len-1];
    delete O[len-1];  //对于数组，这句可以没有，但是对于obj这是必须的
    O.length = len-1;
    return element;
};
Array.prototype.push = function(item1) {
    var O = this ; //toObject
    var lenVal = O.length;
    var n = lenVal; // toUint32;
    var items = [O];var i;
    for(i = 0; i < arguments.length; i++) {
        items.push(arguments[i]); // 标准里没有使用push实现这个
    }
    while(items.length > 0){
        var E = items.shift();
        O[n] = E;
        n++;
    }
    O.length = n;
    return n;
};
Array.prototype.reverse = function () {
    var O = this;  //toObject
    var lenVal = O.length;
    var len = lenVal; // toUint32
    var middle = Math.floor(len/2);
    var lower = 0;
    while(lower !== middle){ //不知道为什么标准里写的不是 lower >= middle
        var upper = len - lower - 1;
        var lowerValue = O[lower];
        var upperValue = O[upper];
        //这一段处理真正的体现了这个函数不止限于Array调用
        if(O.hasOwnPropery(lower.toString()) && O.hasOwnPropery(upper.toString())){
            O[lower] = upperValue;
            O[upper] = lowerValue;
        }else if(!O.hasOwnPropery(lower.toString()) && O.hasOwnPropery(upper.toString())){
            O[lower] = upperValue;
            delete O[upper] ;
        }else if( O.hasOwnPropery(lower.toString()) && !O.hasOwnPropery(upper.toString()) ){
            delete O[lower];
            O[upper] = lowerValue;
        }
        lower++;
    }
    return O;
};
Array.prototype.shift = function (){
    var O = this; //toObject
    var lenVal = O.length;
    var len = lenVal; // toUint32
    if(len === 0) { O.length = 0; return undefined; }
    var first = O[0];
    var k =1;
    while(k < len){
        var from = k.toString();
        if(O.hasOwnPropery(from)){
            O[k-1] = O[k];
        }else{
            delete O[k-1];
        }
        k++;
    }
    delete O[k-1];
    O.length = len - 1;
    return first;
};
Array.prototype.slice = function (start, end){
    var O = this; //toObject
    var A = [];
    var lenVal = O.length;
    var len = lenVal; // toUint32
    var relativeStart = start; // toIntger
    var k = relativeStart < 0 ? Math.max(len + relativeStart,0):Math.min(relativeStart,len);
    var relatveEnd = typeof end ==="undefined" ? len : end; // toIntger(end)
    var final_ = relatveEnd < 0 ? max(len+relatveEnd,0) : min(relativeEnd,len);
    var n  = 0;
    while(k < final_){
        if(O.hasOwnPropery(k.toString())){
            A[n] = O[k];
        }
        k++;n++;
    }
    return A;
};
Array.prototype.sort = function (){
};
Array.prototype.splice = function (start,deleteCount) {
    var O = this; //toObject
    var A = [];
    var lenVal = O.length;
    var len = lenVal; // toUint32
    var relatveStart = start; // toIntger
    var actualStart = relatveStart < 0 ? Math.max(len + relatveStart,0): Math.min(relatveStart,len);
    var actualDeleteCount = Math.min(max(deleteCount,0),len - actualStart); //toIntger(deleteCount)
    var k = 0;
    var from;
    while(k < actualDeleteCount){
        from = (actualStart+k).toString();
        if(O.hasOwnPropery(from)){
            A[k] = O[actualStart+k];
        }
        k++;
    }
    var items = [];
    for(i = 2; i < arguments.length; i++) {
        items.push(arguments[i]); // 标准里没有使用push实现这个
    }
    var itemCount = items.length;
    if(itemCount < actualDeleteCount){
        k = actualStart;
        while(k < len - actualDeleteCount){
            from = (k+actualDeleteCount).toString();
            if(O.hasOwnPropery(from)){
                O[k+itemCount] = O[k+actualDeleteCount];
            }else{
                delete O[k+itemsCount];
            }
            k++;
        }
        k = len;
        while(k > len-actualDeleteCount + itemsCount){
            delete O[k-1];
            k--;
        }
    }else if(itemCount > actualDeleteCount){
        k = len - actualDeleteCount;
        while(k > actualStart){
            from = (k+actualDeleteCount - 1).toString();
            if(O.hasOwnPropery(from)){
                O[k + itemCount - 1] = O[k+actualDeleteCount -1];
            }else{
                delete O[k + itemCount -1];
            }
            k--;
        }
    }
    k = actualStart;
    while(items.length>0){
        var E = items.shift();
        O[k] = E;
        k++;
    }
    O.length = len - actualDeleteCount + itemCount;
    return A;
};
Array.prototype.unshift = function (item1){
    var O = this; //toObject
    var lenVal = O.length;
    var len = lenVal; // toUint32
    var argCount = arguments.length;
    var k = len;
    while(k > 0){
        var from = (k-1).toString();
        if(O.hasOwnPropery(from)){
            O[k + argCount - 1] = O [k-1];
        }else{
            delete O[k + argCount -1];
        }
        k--;
    }
    var j = 0;
    var items = []; var i;
    for(i = 0 ; i < arguments.length;i++){
        items.push(arguments[i]);
    }
    while(items.length > 0){
        var E = items.shift();
        O[j] = E;
        j++;
    }
    O.length = len + argCount;
    return len+argCount;
};
Array.prototype.indexOf = function (searchElement){
    var O = this; //toObject
    var lenVal = O.length;
    var len = lenVal; // toUint32
    if(len === 0) { return -1; }
    var n = arguments.length >=2 ? arguments[1] : 0; //toIntger(arguments[1])
    if(n > len) { return -1; }
    var k = n>=0 ? n : Math.max(len + n, 0);
    while(k < len){
        if(O.hasOwnPropery(k.toString)()){
            if(searchElement === O[k]) {
                return k;
            }
        }
        k++;
    }
    return -1;
};
Array.prototype.lastIndexOf = function (searchElement){
    var O = this;
    var lenVal = O.length;
    var len = lenVal; // toUint32
    if(len === 0) { return -1; }
    var n = arguments.length >=2 ? arguments[1] : 0; //toIntger(arguments[1])
    var k = n>=0 ? min(n,len-1) : len + n;
    while(k >= 0){
        if(O.hasOwnPropery(k.toString)()){
            if(searchElement === O[k]) {
                return k;
            }
        }
        k++;
    }
    return -1;
};
Array.prototype.every = function (callbackfn){
    var O = this; //toObject
    var lenVal = O.length;
    var len = lenVal; // toUint32
    //if(! callbackfn instanceof Function) {throw "typeerror"}
    var T = arguments.length >=2 ? arguments[1] : undefined;
    var k = 0;
    while(k < len){
        if(O.hasOwnPropery(k.toString())){
            var testResult = callbackfn.call(T,O[k],k,O);
            if(!testResult) { return false; }
        }
        k++;
    }
    return true;
};
Array.prototype.some = function (callbackfn){
    var O = this; //toObject
    var lenVal = O.length;
    var len = lenVal; // toUint32
    //if(! callbackfn instanceof Function) {throw "typeerror"}
    var T = arguments.length >=2 ? arguments[1] : undefined;
    var k = 0;
    while(k < len){
        if(O.hasOwnPropery(k.toString())){
            var testResult = callbackfn.call(T,O[k],k,O);
            if(testResult) { return true; }
        }
        k++;
    }
    return false;
};
Array.prototype.forEach = function(callbackfn){
    var O = this; //toObject
    var lenValue = O.length;
    var len = lenVal; // toUint32
    //if(! callbackfn instanceof Function) {throw "typeerror"}
    var T = arguments.length >=2 ? arguments[1] : undefined;
    var k = 0;
    while( k < len ){
        if(O.hasOwnPropery(k.toString())){
            callbackfn.call(T,O[k],k,O);
        }
        k++;
    }
    return undefined;
};
Array.prototype.map = function(callbackfn){
    var O = this; //toObject
    var lenValue = O.length;
    var len = lenVal; // toUint32
    //if(! callbackfn instanceof Function) {throw "typeerror"}
    var T = arguments.length >=2 ? arguments[1] : undefined;
    var A = [];
    var k = 0;
    while( k < len ){
        if(O.hasOwnPropery(k.toString())){
            A[k] = callbackfn.call(T,O[k],k,O);
        }
        k++;
    }
    return A;
};
Array.prototype.filter = function(callbackfn){
    var O = this; //toObject
    var lenValue = O.length;
    var len = lenVal; // toUint32
    //if(! callbackfn instanceof Function) {throw "typeerror"}
    var T = arguments.length >=2 ? arguments[1] : undefined;
    var A = [];
    var k = 0;
    var to = 0;
    while( k < len ){
        if(O.hasOwnPropery(k.toString()) ){
            if( callbackfn.call(T,O[k],k,O) ){
                A[to] = O[k];
                to++;
            }
        }
        k++;
    }
    return A;
};
Array.prototype.reduce = function (callbackfn){
    var O = this; //toObject
    var lenValue = O.length;
    var len = lenVal; // toUint32
    //if(! callbackfn instanceof Function) {throw "typeerror"}
    //if(len === 0 && arguments.length <  2 ) {throw "type error"}
    var k = 0;
    var accumulator;
    if(arguments.length > 2) { 
        accumulator = arguments[1]; 
    }else{
        var kPresent = false;
        while(kPresent === false && k < len){
            kPresent = O.hasOwnPropery(k.toString());
            if(kPresent){
                accumulator = O[k];
            }
            k++;
        }
        // if(!kPresent) {throw "type error"}
    }
    while( k < len ){
        if(O.hasOwnPropery(k.toString())){
            accumulator = callbackfn.call(undefined,accumulator,O[k],k,O);
        }
        k++;
    }
    return accumulator;
};
Array.prototype.reduceRight = function (callbackfn){
    var O = this; //toObject
    var lenValue = O.length;
    var len = lenVal; // toUint32
    //if(! callbackfn instanceof Function) {throw "typeerror"}
    //if(len === 0 && arguments.length <  2 ) {throw "type error"}
    var k = len - 1;
    var accumulator;
    if(arguments.length > 2) { 
        accumulator = arguments[1]; 
    }else{
        var kPresent = false;
        while(kPresent === false && k >= 0){
            kPresent = O.hasOwnPropery(k.toString());
            if(kPresent){
                accumulator = O[k];
            }
            k--;
        }
        // if(!kPresent) {throw "type error"}
    }
    while( k >= 0 ){
        if(O.hasOwnPropery(k.toString())){
            accumulator = callbackfn.call(undefined,accumulator,O[k],k,O);
        }
        k--;
    }
    return accumulator;
};
{% endhighlight %}
