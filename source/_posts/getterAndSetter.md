---
title: js对象属性的getter与setter
date: 2016-12-29 10:55:24
tags: ['javascript','前端']
categories: [笔记]
---

## 总述

我们知道对象是由名字、值和一组特性（attribute）构成的。在ECMAScript5.1中，属性值可以用一个或两个方法替代，这两个方法就是`getter`和`setter`。由`getter`和`setter`定义的属性称作“存取器属性”（accessor property），它不同于“数据属性”（data property），数据属性只有一个简单的值。

- 当程序查询存取器属性的值时，调用`getter`方法(无参)
- 当程序设置一个存取器属性的值时，调用`setter`方法，赋值表达式右侧的值作为参数传入`setter`

存取器属性不具有可写性。如果属性同时拥有`getter`与`setter`，那么它是一个读/写属性。如果只有`getter`（或者`setter`），那么他就是一个只读（写）属性。读取只写属性总是返回	`undefined`

## 定义setter和getter的几种方式

## 在新对象初始化时定义（ES5.1）

```js
var obj = {
  value:1,
  get access_prop(){
     return this.value
  },
  set access_prop(value){
     this.value = value
  }
}

console.log(obj.access_prop)//1
obj.access_prop = 2
console.log(obj.access_prop,obj.value)//2 2
```
<!-- more -->

<a class="jsbin-embed" href="http://jsbin.com/sutoha/7/embed?js,console">JS Bin on jsbin.com</a><script src="http://static.jsbin.com/js/embed.min.js?3.40.2"></script>

<p class="tip">在`getter`或`setter`中对改存取器属性进行操作会形成死循环最终导致内存溢出错误</p>

<a class="jsbin-embed" href="http://jsbin.com/vifakec/embed?js,console">JS Bin on jsbin.com</a><script src="http://static.jsbin.com/js/embed.min.js?3.40.2"></script>

### 使用get语法时应注意以下问题

- 可以使用数值或字符串作为标识
- 必须不带参数 (请参考Incompatible ES5 change: literal getter and setter functions must now have exactly zero or one arguments)
- 在对象字面量中,同一个属性不能有两个get,也不能既有get又有属性键值对(不允许使用 { get x() { }, get x() { } } 和 { x: ..., get x() { } } )

### 可以使用变量的值作为存取器属性名

<p class="tip">注意: 计算后的属性为体验性的技术, 作为 ECMAScript 6 的一部分. 在不支持的环境中使用会引发一个语法错误.</p>

```js
var expr = "foo";

var obj = {
  get [expr]() { return "bar"; }
};

console.log(obj.foo); // "bar"
```

## 使用Object.create方法创建

> Object.create方法创建一个拥有指定原型和若干个指定属性的对象。[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/create)

### 语法

```js
Object.create(proto, [ propertiesObject ])
```

### 参数

- proto
  一个对象，作为新创建对象的原型。
- propertiesObject
  可选。该参数对象是一组属性与值，该对象的属性名称将是新创建的对象的属性名称，值是属性描述符（这些属性描述符的结构与Object.defineProperties()的第二个参数一样）。注意：该参数对象不能是 undefined，另外只有该对象中自身拥有的可枚举的属性才有效，也就是说该对象的原型链上属性是无效的。

```js
var obj = Object.create(Object.prototype,{
  bar: {
    get: function(){
      return 10;
    },
    set: function (val) {
      console.log("Setting `obj.bar` to "+val);
    }
  }
});
console.log(obj.bar); //12
obj.bar = 12;//"Setting `obj.bar` to 12"
console.log(obj)
```
> 控制台打印出的对象结构为:
![](http://7xrqm7.com1.z0.glb.clouddn.com/getter&setter_object.create.png)

<p class="tip">对象的数据属性包含4个特性，分别是值（value）、可写性（writable）、可枚举性（enumerable）和可配置性（configurable）。存取器属性不具有值特性和可写性，它们的可写性是有setter的存在与否决定的。存取器属性的4个特性分别是：读（get）、写（set）、可枚举性和可配置性。
</p>

### 可以为存取器属性设置特性

```js
var obj = Object.create(Object.prototype,{
  bar : {
    get : function(){
      return 10;
    },
    set : function (val) {
      console.log("Setting `obj.bar` to "+val);
    },
		configurable : true
  }
});
console.log(obj.bar); //12
obj.bar = 12;//"Setting `obj.bar` to 12"
console.log(obj)
```

## 使用 Object.defineProperty 方法创建

> Object.defineProperty() 方法会直接在一个对象上定义一个新属性，或者修改一个已经存在的属性， 并返回这个对象。 [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)

### 语法

```js
Object.defineProperty(obj, prop, descriptor)
```

### 参数

- obj
  需要定义属性的对象。
- prop
  需定义或修改的属性的名字。
- descriptor
  将被定义或修改的属性的描述符。

### 返回值
- 返回传入函数的对象，即第一个参数obj

### 创建getter&setter

```js
var obj = { a : 1}//声明一个对象,包含一个 a 属性,值为1
    Object.defineProperty(obj,"b",{
        get: function () {
            return this.a;
        },
        set : function (val) {
            this.a = val;
        },
        configurable : true
    });

    console.log(obj.b);//1
    obj.b = 2;
    console.log(obj.b);//2
```
该方法与`Object.create`差别不大，前两种方法只能在声明定义时指定getter与setter，`Object.defineProperty`可以随时添加和修改

## 使用 Object.defineProperties 方法创建

> Object.defineProperties() 方法在一个对象上添加或修改一个或者多个自有属性，并返回该对象。 [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperties)

### 语法
```js
Object.defineProperties(obj, props)
```

### 参数
- obj
  将要被添加属性或修改属性的对象
- props
  该对象的一个或多个键值对定义了将要为对象添加或修改的属性的具体配置

### 返回值
- 返回传入函数的对象，即第一个参数obj

```js
var obj = {a:1,b:2};
    Object.defineProperties(obj,{
        "A":{
            get:function(){return this.a;},
            set:function(val){this.a = val;}
        },
        "B":{
            get:function(){return this.b;},
            set:function(val){this.b = val}
        }
    });

    console.log(obj.A);
    console.log(obj.B);
    obj.A = 3;
    obj.B = "hello";
    console.log(obj.A);
    console.log(obj.B);
```
该方法是`Object.defineProperty`的批量版本

## 使用 Object.prototype.__defineGetter__ 以及 Object.prototype.__defineSetter__ 方法

<p class="tip">已废弃
该特性已经从 Web 标准中删除，虽然一些浏览器目前仍然支持它，但也许会在未来的某个时间停止支持，请尽量不要使用该特性。</p>
