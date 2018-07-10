这篇笔记回顾es5函数和面向对象部分的内容。是根据[learning javascript](http://speakingjs.com/)这本书整理。

函数扮演的角色有三种：

+ 普通函数 函数名是小些
+ 构造器 函数名首字母大写
+ 对象的方法 函数变成对象的一个属性

parameter和argument的区别
+ 前者用来定义函数
+ 后者用来调用函数

函数定义有三种方式：
+ 函数表达式  将function object赋给变量

```javasript
var add  = function h(x, y) {
    return x + y
}
```
+ 函数声明

```javascript
function add(x, y) {
    return x + y
}

// that equals to
var add = function(x, y) {
    return x + y
}
```

+ 通过函数构造器

```javascript
var add = new Function('x', 'y', 'return x + y')
```

实际上所有的函数都是Function 对象的实例。

### 作用域提升

函数声明会被全局提升，变量提升只是局部的。

```javascript
add(1, 2) // this is ok

function add(x, y) {
    return x + y
}
```

```javascript
add(1, 2) // this will not work, and raise a TypeError

var add = function(x, y) {
    return x + y
}

// that is

var add
add(1, 2)

add = function(x, y) {
    return x + y
}
```

### 函数名称

```javascript
function add(x, y) {
    return x + y
}
add.name   // 'add' 


var add = function(x, y) {
    return x + y
}

add.name // ''

var add = function h(x, y) {
    return x + y
}

add.name // 'h'
```

综上，使用第一种方式是最好的，即能全局提升作用域又有函数名称。

### 更好的调用函数

有三个方法可以用来更好的调用函数：

+ apply(thisValue, [arg1, arg2, arg3])  当参数是类数组形式的时候更方便
+ bind(thisValue, arg1, arg2)  用来生成偏函数

```javascript
function add(x, y) {
    return x + y
}

var add1 = add.bind(null, 1)
add1(3) // 4
```

+ call(thisValue, arg1, arg2, arg3)  重新设置this，可以用来借用方法

### 关于参数

#### 必须参数

可以通过三种方法做到:

```javascript
// first
if (mandatory === undefined) {
        throw new Error('Missing parameter: mandatory');
}

// second
if (!mandatory) {
    throw new Error('Missing parameter: mandatory');
}

//third
if (arguments.length < 1) {
    throw new Error('You need to provide at least 1 argument');
}

```

第三种方法不能识别foo(undefined)情况

#### 可选参数

可以通过四种方法；

```javascript
function bar(arg1, arg2, optional) {
    if (optional === undefined) {
        optional = 'default value';
    }
}

if (!optional) {
    optional = 'default value';
}

optional = optional || 'default value';

if (arguments.length < 3) {
    optional = 'default value';
}

```

同样第四种方法也有同样的问题。

#### 如何传入引用参数

也就是传入的是引用，在函数内部更改参数值会改变原始值，可以通过把参数值包裹在数组中传入。

注意函数之间的参数对应关系，一个典型的陷阱：

```javascript
 [ '1', '2', '3' ].map(parseInt) // [1, NaN, NaN]

// because the signiture of parameter of map() if function (element, index, array)
// but the signiture of parseInt is parseInt(string, radix?)
```
#### 命名参数

js本来是不支持命名参数的，可以通过传入对象字面量实现。

```javascript
function selectEntries(options) {
    options = options || {};
    var start = options.start || 0;
    var end = options.end || getDbLength();
    var step = options.step || 1;
    ...
}
selectEntries({ start: 3, end: 20, step: 2 });
```

这种模式叫做__options__或者__option obeject__。


## 作用域

只有函数才会引入新的作用域。所有变量的声明都会被提升到直接作用域的顶部，举个例子：

```javascript
function f() {
    console.log(bar);  // undefined
    var bar = 'abc';
    console.log(bar);  // abc
}
```

当作用域嵌套时，内部作用域可以获取外部作用域的变量，但是会被相同名字的内部变量遮蔽。

```javascript
var x = "global";
function f() {
    var x = "local";
    console.log(x); // local
}
f();
console.log(x); // global
```

有一个常见的问题是，没有声明直接给一个变量赋值，这样会导致这个变量成为全局变量。

```javascript
function local(){
    x = 1
}
x // 1
```

前面说到，只有函数才能引入新的作用域，但是我们很多时候不希望在if,for,while等块中出现的临时变量在其他地方也可以访问，其他语言默认就是这样的，在js中，我们一般通过IIFE来实现。比如：

```javascript
function f() {
    if (condition) {
        (function () {  // open block
            var tmp = ...;
            ...
        }());  // close block
    }
}

```

## 面向对象编程

js的面向对象有四层含义：

+ 通过一个Object
+ 通过原型链
+ 用构造函数来实例化
+ 子类与继承


### 第一层，对象字面量

```javascript
var jane = {
    name: 'Jane',

    describe: function () {
        return 'Person named '+this.name;
    }
};
```

可以通过.操作符或者[]计算key来增删改查对象属性。

除了直接声明对象字面量，还可以通过Object构造函数。

### this

函数中的隐式参数this指向的是函数调用者。因此从对象字面量中抽取其方法是要注意this的丢失，可以用bind方法绑定正确的this。

一个典型的问题是在for循环中丢失this值。

```javascript
var obj = {
    name: 'Jane',
    friends: [ 'Tarzan', 'Cheeta' ],
    loop: function () {
        'use strict';
        this.friends.forEach(
            function (friend) {  // (1)
                console.log(this.name+' knows '+friend);  // (2)
            }
        );
    }
};
```

如何在(2)中获取正确的this值有以下几种方法：

+ 用变量代替this

```javascript
loop: function () {
    'use strict';
    var that = this;
    this.friends.forEach(function (friend) {
        console.log(that.name+' knows '+friend);
    });
}
```

+ 用bind方法绑定

```javascript
loop: function () {
    'use strict';
    this.friends.forEach(function (friend) {
        console.log(this.name+' knows '+friend);
    }.bind(this));  // (1)
}
```

+ 直接在forEach方法中传递this参数

```javascript
loop: function () {
    'use strict';
    this.friends.forEach(function (friend) {
        console.log(this.name+' knows '+friend);
    }, this);
}
```

前两种是比较通用的方法。

### 通过原型链继承

```javascript
var proto = {
    describe: function () {
        return 'name: '+this.name;
    }
};
var obj = {
    [[Prototype]]: proto,
    name: 'obj'
};
```
原型对象中的this永远指向发起搜索对象。也就是说：

```javascript
obj.describe()
'name: obj'
```

重新定义相同名字的属性进行重写：

```javascript
obj.describe = function () { 
    return 'overridden' 
};
obj.describe() // 'overridden'
```

也可以通过原型链共享数据：

```javascript
var jane = {
    name: 'Jane',
    describe: function () {
        return 'Person named '+this.name;
    }
};
var tarzan = {
    name: 'Tarzan',
    describe: function () {
        return 'Person named '+this.name;
    }
};
```

像这样的情况就可以抽象出一个“基类”，

```javaascript
var PersonProto = {
    describe: function () {
        return 'Person named '+this.name;
    }
};
var jane = {
    [[Prototype]]: PersonProto,
    name: 'Jane'
};
var tarzan = {
    [[Prototype]]: PersonProto,
    name: 'Tarzan'
};

```

也可以通过给定原型链创建对象：
```javascript
var PersonProto = {
    describe: function () {
        return 'Person named '+this.name;
    }
};
var jane = Object.create(PersonProto, {
    name: { value: 'Jane', writable: true }
});
```

可以通过`__proto__`来访问对象的原型对象。

遍历本身对象属性有两个方法：

+ `Object.getOwnPropertyNames(obj)`  获取本身所有属性的key
+ `Object.keys(obj) ` 获取本身所有可迭代属性的key

如果想获取所有属性包括原型链上的属性，可以使用：

+ `for in`循环

```javascript
for («variable» in «object»)
    «statement»
```

+ 递归遍历对象的所有原型链

```javascript
function getAllPropertyNames(obj) {
    var result = [];
    while (obj) {
        // Add the own property names of `obj` to `result`
        result = result.concat(Object.getOwnPropertyNames(obj));
        obj = Object.getPrototypeOf(obj);
    }
    return result;
}

```

同样，如果要检查一个属性是否包含在一个对象中也分两种情况

+ 是否是自身属性 Object.prototype.hasOwnProperty(propKey)
+ 是否是其属性  包含原型链  for in 循环

限制对象属性有三种方法：

+ 禁止扩展 不能在添加新属性
+ seal  所有属性变成unconfigurable
+ freeze  所有属性变成 unconfigurable 和 unwritable

当然，这些变化是shallow的，也就是说不会递归的作用

### 构造函数

上面讲了通过原型链进行继承和数据共享，下面通过构造函数的方式来实现：

```javascript
function Person(name) {
    this.name = name;
}
Person.prototype.describe = function () {
    return 'Person named '+this.name;
};
var jane = new Person('Jane');
jane.describe() // 'Person named Jane'
```

new操作符可以用以下代码实现：

```javascript
function newOperator(Constr, args) {
    var thisValue = Object.create(Constr.prototype); // (1)
    var result = Constr.apply(thisValue, args);
    if (typeof result === 'object' && result !== null) {
        console.log('return returnValue from constructor')
        return result; // (2)
    }
    return thisValue;
}
```
我们可以测试一下：

```javascript

function Con(name) {
    this.name = name
}

Con.prototype.echoName = function() {
    console.log(this.name)
}

// definition of newOperator here

var jane = newOperator(Con, 'zhu');
jane.echoName(); // zhu
```
具体是为什么等会儿解释。

有两个关于构造函数的点要注意以下：

+ 构造函数的原型对象有一个constructor属性指向构造函数
+ 每一个通过new conostructor产生的实例都有一个constructor参数指向构造函数

在编写构造器的时候可以有返回值，返回值就是你想通过new操作生成的实例，因此在上面new的实现代码中会优先返回result，如果没有result就返回thisValue，假设上面的测试代码中把构造函数的返回值改一下：

```javascript
function Con(name) {
    this.name = name
    return new Object();
}

// TypeError: jane.echoName is not a function 
```

最佳实践：不要在原型链中共享数据的初始值

#### 如何实现super

实际上js中的构造函数就类似于其他语言中的类。因此，因此subConstructor就类似于子类，我们可以手动实现别的语言中的super关键字的功能：

```javascript
function Person(name) {
    this.name = name;
}
Person.prototype.describe = function () {
    return 'Person called '+this.name;
};

function Employee(name, title) {
    Person.call(this, name);
    this.title = title;
}
Employee.prototype = Object.create(Person.prototype);
Employee.prototype.constructor = Employee;
Employee.prototype.describe = function () {
    return Person.prototype.describe.call(this)+' ('+this.title+')';
};

var jane = new Employee('Jane', 'CTO');
jane.describe() // Person called Jane (CTO)
jane instanceof Employee // true
jane instanceof Person // true

```

#### 通用方法

即在不用继承的情况下使用别的对象原型链上的方法，类似于duck typing。也就是只要具备类似特征，那么在某些场合下可以认为二者是同一类型。

```javascript
function Wine(age) {
    this.age = age;
}
Wine.prototype.incAge = function (years) {
    this.age += years;
}
Wine.prototype.incAge.call({age:10}, 3) // 13
```
