---
layout: post
title: "JS那些巧妙的数据类型转换--实用版"
categories: 前端开发
tags: javascript 数据类型转换
---

在 ECMAScript5.1 标准中定义了九种数据转换的了函数。大部分javacript引擎都会实现这些数据类型转换的函数。有时候我们在写代码的时候可能会用到这样的数据类型转换。有些数据类型的转换我们可以使用一些巧妙的方式来调用javascript引擎提的数据类型转换函数来实现。以下是实际可用的javacript实现的这九种数据类型转换，与ES5.1标准中定义的函数等价。这[九种数据类型转换函数][ES51sec-9]{:target="_blank"}是：`toPrimitive`、 `toBoolean`、`toNumber`、`toInteger`、`toInt32`、`toUint16`、`toString`、`toObject`，其中`toPrimitive` 和 `toInteger` 为自定义的实现。

{% highlight javascript linenos %}
// type Primitive = Undefined | Null | Boolean | Number | String
// data Hint = undefined | "string" | "number"
// toPrimitive:: (a,Hint) -> Error String Primitive
function toPrimitive(v,hint) 
{
    // isPrimitive:: a -> Boolean
    function isPrimitive(v){
        var type = typeof v;
        return (type === typeof undefined || 
                type === typeof true || 
                type === typeof 0 || 
                type === typeof "" || 
                v === null) ? true : false;
    }

    if(isPrimitive(v)) { return v; }

    if(arguments.length == 1 || typeof hint === typeof undefined) {
        hint = v instanceof Date ? "string" : "number";
    }
    var val;
    if(hint.toLowerCase() == "string"){
        val = v.toString();
        if(isPrimitive(val)) { return val; }
        val = v.valueOf();
        if(isPrimitive(val)) {return val;}
    }else{
        val = v.valueOf();
        if(isPrimitive(val)) {return val;}
        val = v.toString();
        if(isPrimitive(val)) { return val; }
    }
    throw "TypeError";
}
// toBoolean:: a -> Boolean
function toBoolean(v) { return !!v; }
// toNumber:: a -> Number
function toNumber(v) { return +v; }
// toInteger:: a -> Integer
function toInteger(v) 
{
    var number = toNumber(v);
    if(isNaN(number)) { return +0; }
    if(number === 0 || !isFinite(number)){ return number; }
    if(number >= 0 ) {
        return Math.floor(number);
    }else{
        return Math.ceil(number);
    }
}
// toInt32:: a -> Int32
function toInt32(v) { return v>>0; }
// toUint32:: a -> Uint32
function toUint32(v) { return v>>>0; }
// toUint16:: a -> Uint16
function toUint16(v) { return String.fromCharCode(v).charCodeAt(0); }
// toString:: a -> String
function toString(v) { return ""+v; }
// toObject:: a -> Error String Object
function toObject(v)
{
    if(typeof v === typeof undefined || v === null) {
        throw "TypeError";
    }
    return Object(v);
}
{% endhighlight %}

[ES51sec-9]: http://ecma-international.org/ecma-262/5.1/index.html#sec-9
