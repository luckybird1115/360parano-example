---
title: 前端基础复习-JavaScript-2
date: 2020-04-03 18:43:24
tags: js基础
categories: 前端
---
前端自检清单JavaScript基础2--原型原型链
<!--more-->
### 原型和原型链

#### 1.理解原型设计模式以及`JavaScript`中的原型规则

**原型模式** ：是指原型实例指向创建对象的种类，并通过拷贝这些原型创建新的对象，是一种用来创建对象的模式，也就是创建一个对象作为另一个对象的prototype属性。

创建方式：**Object.create(prototype, optionalDescriptorObjects)** ②prototype

 **原型原则**：

①所有的引用类型（数组，对象，函数）都有对象属性，都可以自由扩展属性。null除外；

②所有的引用类型（数组，对象，函数）都有对象属性，对象均有`__proto__`属性，属性值是一个普通的对象，`__proto__`的含义是隐式原型;

③所有的函数，均有一个prototype属性，属性值是一个普通的对象，`prototype`的含义是显式原型;

④所有的引用类型（数组，对象，函数）的属性`__proto__`指向它的构造函数prototype值

⑤当获得一个对象的属性时，首先在对象本身查找，如果没有会去它的`__proto__`中查找

#### 2.`instanceof`的底层实现原理，手动实现一个`instanceof`

将对象的`__proto__`指向与构造函数的prototype判断

```js
function new_instance_of(leftVaule, rightVaule) { 
    let rightProto = rightVaule.prototype; // 取右表达式的 prototype 值
    leftVaule = leftVaule.__proto__; // 取左表达式的__proto__值
    while (true) {
    	if (leftVaule === null) {
            return false;	
        }
        if (leftVaule === rightProto) {
            return true;	
        } 
        leftVaule = leftVaule.__proto__ 
    }

```

#### 4.实现继承的几种方式以及他们的优缺点

**构造函数**：`Parent.call(this)`，优点：可以继承父类属性，避免引用类型的属性被所有子类共享，可以向父类构造函数里传参；缺点：无法继承Parent原型链上的属性和方法，②方法都在构造函数中定义，每次创建实例都会创建一遍方法

**原型链继承**：`Child.prototype=new Parent()`把prototype设置为父类的实例，缺点：①无法向父类构造函数里传参数②父类原型上的引用属性被所有子类共享，

**组合继承**:可以继承父类属性及原型链属性，也可向父类构造函数传参数，融合了构造函数和原型链继承的优点，缺点：父类构造函数被调用了两次

```
function Child(){
   Parent.call(this)
}
Child.prototype=new Parent();
//优化
function Child(){
   Parent.call(this)
}
Child.prototype=Parent.prototype;//缺点：因为原型上有个属性constructor，子类的实例constructor也是parent，无法区分实例对象是父类还是子类，子类原型上添加属性影响父类

```

**寄生继承**: 优点可以添加方法和属性

```
function createChild(p){
   var o=Object.create(p);
   o.newMethod=function(){
   }
   return o;
}
```

**寄生组合继承**

```
function object(o) {
  function F() {}
  F.prototype = o;
  return new F();
}
function inheritPrototype(subClass,superClass){
	var p=object(superClass.prototype);
	p.constructor=subClass;
	subClass.prototype=p;
}
function Child(){
   Parent.apply(this,arguments);
}
inheritPrototype(Child, Parent);
```

优点：只调用了一次Parent构造函数，并且因此避免了在 Parent.prototype 上面创建不必要的、多余的属性。与此同时，原型链还能保持不变；

**ES6 class** **extends**

```js
class Parent {
    constructor(x,y){
        this.x = x;
        this.y = y;
    }
    toString(){

    }
}

class Child extends Parent{
    constructor(x,y,color){
        super(x,y);
        this.color = color;
    }
    toString(){
        return super.toString();
    }
}
/*
* 通过extends关键字继承了Parent类所有的属性和方法。
* 子类的constructor方法 和 toString方法中都出现了super关键字,
* 在这里表示父类的构造函数，用来新建父类的this对象。
* 子类必须在constructor方法中调用super方法，因为子类的this对象必须通过父类的构造函数完成创建，
* 并且得到与父类同样的实例属性和方法。如果不调用，子类就得不到this对象，就不能进行二次加工。
* 要注意的一个地方是，在子类的构造函数中，只有调用super才可以使用this关键字，
* 所以this关键字要放到super关键字之后，
* 是因为子类实例的构建，基于父类实例，只有super方法能调用父类实例。
*/
/*
* ES5的继承实质是先创建子类的实例对象this，然后再将父类的方法添加到this上面(Parent.apply(this))
* ES6的继承实质是先将父类实例对象的属性和方法加到this上面(所以需先调用super方法)，然后再用子类构造函数修改this
*/

class Child extends Parent{

}
//等同于
class Child extends Parent{
    constructor(...args){
        super(...args);
    }
}
/*
* 如果子类没有定义constructor方法，这个方法会被默认添加，
* 也就是说不管有没有显示的定义，子类都有constructor方法。
*/
class Parent {

}

class Child extends Parent{
    constructor(){
        super();
    }
}

/*
* super这个关键字既可以当做函数使用，也可以当做对象使用
* 作为函数调用时，代表父类的构造函数，子类构造函数必须执行一次super函数
* super虽然代表了父类Parent的构造函数，但是返回的是子类Child的实例
* 即super内部的this指的是Child，super在这里就相当于：
* Parent.prototype.constructor.call(this)
* super作为函数使用时，super()只能用在子类的构造函数中*/

class Parent {
    outPut(){
        console.log(1)
    }
}

class Child extends Parent{
    constructor(){
        super();
        console.log(super.outPut())
    }
}

/*
* super作为对象时，在普通方法中指向父类的原型对象，静态方法中指向父类
* super在普通方法中代表Parent.prototype,所以super.outPut()就相当于
* Parent.prototype.outPut()
* 注意，父类实例上的方法或属性是无法通过super调用的*/
```



#### 5.至少说出一种开源项目(如`Node`)中应用原型继承的案例



#### 6.可以描述`new`一个对象的详细过程，手动实现一个`new`操作符

①创建一个空对象

②将父类构造函数this指针指向创建的Object

③如何父类构造函数有返回的对象，返回该对象，否则，就返回新建的对象

```
function myNewOpt(Obj,args){
    var o=Object.create(Obj.prototype);
    Obj.apply(o,args);
    return o;
}
```



#### 7.理解`es6 class`构造以及继承的底层实现原理

```
class Parent {
    constructor(name,age){
        this.name = name;
        this.age = age;
    }
    speakSomething(){
        console.log("I can speek chinese");
    }
}
```

class在babel编译后，

```
"use strict";

var _createClass = function () {
    function defineProperties(target, props) {
        for (var i = 0; i < props.length; i++) {
            var descriptor = props[i];
            descriptor.enumerable = descriptor.enumerable || false;
            descriptor.configurable = true;
            if ("value" in descriptor) descriptor.writable = true;
            Object.defineProperty(target, descriptor.key, descriptor);
        }
    }

    return function (Constructor, protoProps, staticProps) {
        if (protoProps) defineProperties(Constructor.prototype, protoProps);
        if (staticProps) defineProperties(Constructor, staticProps);
        return Constructor;
    };
}();

function _classCallCheck(instance, Constructor) {
    if (!(instance instanceof Constructor)) {
        throw new TypeError("Cannot call a class as a function");
    }
}

var Parent = function () {
    function Parent(name, age) {
        _classCallCheck(this, Parent);

        this.name = name;
        this.age = age;
    }

    _createClass(Parent, [{
        key: "speakSomething",
        value: function speakSomething() {
            console.log("I can speek chinese");
        }
    }]);

    return Parent;
}();
```

在子类构造时，先调用父类构造函数设置this的属性和方法，然后子类构造中扩展修改this，底层实现使用了寄生组合继承

**继承**

```js
class Parent {
    static height = 12
    constructor(name,age){
        this.name = name;
        this.age = age;
    }
    speakSomething(){
        console.log("I can speek chinese");
    }
}
Parent.prototype.color = 'yellow'


//定义子类，继承父类
class Child extends Parent {
    static width = 18
    constructor(name,age){
        super(name,age);
    }
    coding(){
        console.log("I can code JS");
    }
}

var c = new Child("job",30);
c.coding()
```

转码之后的代码变成了这样

```js
"use strict";

var _createClass = function () {
    function defineProperties(target, props) {
        for (var i = 0; i < props.length; i++) {
            var descriptor = props[i];
            descriptor.enumerable = descriptor.enumerable || false;
            descriptor.configurable = true;
            if ("value" in descriptor) descriptor.writable = true;
            Object.defineProperty(target, descriptor.key, descriptor);
        }
    }

    return function (Constructor, protoProps, staticProps) {
        if (protoProps) defineProperties(Constructor.prototype, protoProps);
        if (staticProps) defineProperties(Constructor, staticProps);
        return Constructor;
    };
}();

function _possibleConstructorReturn(self, call) {
    if (!self) {
        throw new ReferenceError("this hasn't been initialised - super() hasn't been called");
    }
    return call && (typeof call === "object" || typeof call === "function") ? call : self;
}

function _inherits(subClass, superClass) {
    if (typeof superClass !== "function" && superClass !== null) {
        throw new TypeError("Super expression must either be null or a function, not " + typeof superClass);
    }
    subClass.prototype = Object.create(superClass && superClass.prototype, {
        constructor: {
            value: subClass,
            enumerable: false,
            writable: true,
            configurable: true
        }
    });
    if (superClass) Object.setPrototypeOf ? Object.setPrototypeOf(subClass, superClass) : subClass.__proto__ = superClass;
}

function _classCallCheck(instance, Constructor) {
    if (!(instance instanceof Constructor)) {
        throw new TypeError("Cannot call a class as a function");
    }
}

var Parent = function () {
    function Parent(name, age) {
        _classCallCheck(this, Parent);

        this.name = name;
        this.age = age;
    }

    _createClass(Parent, [{
        key: "speakSomething",
        value: function speakSomething() {
            console.log("I can speek chinese");
        }
    }]);

    return Parent;
}();

Parent.height = 12;

Parent.prototype.color = 'yellow';

//定义子类，继承父类

var Child = function (_Parent) {
    _inherits(Child, _Parent);

    function Child(name, age) {
        _classCallCheck(this, Child);

        return _possibleConstructorReturn(this, (Child.__proto__ || Object.getPrototypeOf(Child)).call(this, name, age));
    }

    _createClass(Child, [{
        key: "coding",
        value: function coding() {
            console.log("I can code JS");
        }
    }]);

    return Child;
}(Parent);

Child.width = 18;


var c = new Child("job", 30);
c.coding();
```

原理：

```
function F(){}
F.prototype = superClass.prototype
subClass.prototype = new F()
subClass.prototype.constructor = subClass
```