设置es6开发环境的笔记：

使用babel之类的编译器将es6代码以及各种浏览器不能直接支持的版本的代码转换为es5代码。具体有三种格式： amd commonjs es6 module loader api 这三种形式，这是历史原因决定的。


选择一个包管理器:

1. npm 主要服务器端的包 专门为nodejs设计  但是由于browserify和webpack等工具的存在，
2. bower 客户端的包
3. jspm

选择一种模块系统（主要是为了在模块化便携代码的同时还能被浏览器理解）
1. requirejs for amd modules
2. browerify  for commonjs modules
3. webpack for all
4. systemjs  for all

选择一种linter and checker:
1. jshint
2. jslint
3. eslint  主流
4. jscs

选择一种typechecker:
1. flow from facebook
2. typescript from microsoft
3. Closure compiler from  google

选择一种测试工具：
jest mocha jasmine
可以通过babel配置实现

选择shims/poyfills
1. es6-shim
2. core.js

选择一个文档工具
1. jsdoc
2. esdoc
3. documentation.js


es6包含三种feature:
1. 已有feature的语法改善  class modules
2. 给标准库添加 新功能 给string array添加新方法 promise map set
3. 全新的feature  let, const, symbol, generator, weakmaps

如何使用babel，静态转换和动态转换区别？
1. 客户端静态转换
2. 服务器端动态转换
3. 服务器端静态转换

source maps:
将编译前的es6代码和编译后的代码对应起来，方便调试

浏览器端设置
wbepack: 客户端的module loader and module builder

babelhelper作用：去掉一些编译后的重复的工具函数的声明，使用全局的babelhelper


exploring javascript:

emacascript 标准由tc39制定，每个feature有四个不同的阶段：

+ sketch  description for feature
+ proposal
+ implementation
+ standard


es6的目标，
+ 为了便携更复杂的应用，需要模块化
+ 创建库更方便
+ 版本

其实叫emca2015更加合适，因为在此之后，每一年都会有一个版本，比如之后的叫es2016,es2017，实际上已经开始接受了。
有很多语法糖似的特性，是从python，ruby借鉴而来，对于普通程序员来说，用不到这么多特性，但是这些特性还是有积极意义的。
像generator，,iterator,proxies这些特性是给创建库的程序员创造的，对于普通使用库的程序员来说确实是用不太到的。

没有列表推导，可以通过函数更好实现？

本身没有类型检查，通过microsoft typescript或者facebook flow实现

es6是向前兼容的?

核心的es6特性：

+ 第一个就是关于var, let, const的使用问题。
建议： 对于不会改变的值用const，会改变的用let, let的行为其实和其他语言的int, doudle这些一样，但是最好不用var。

+ 再也不用使用iife了，用block代替，即：
```js
{ //open block
    let temp = 0
} // block
console.log(temp)
```

+ 在字符串中插入变量不用拼接啦， 直接用模板字符串做字符串插值

```js
// es5
function printCoord(x, y) {
    console.log('('+x+', '+y+')');
}

// es6
function printCoord(x, y) {
    console.log(`(${x}, ${y})`);
}
```

这个特性还可以用来展示多行字符串

+ 在es5中，我们经常要注意this的问题，特别是在回调函数中使用this的时候，而在es6中的箭头函数就解决了这个问题：

```js
// es5
function UiComponent() {
    var _this = this; // (A)
    var button = document.getElementById('myButton');
    button.addEventListener('click', function () {
        console.log('CLICK');
        _this.handleClick(); // (B)
    });
}
UiComponent.prototype.handleClick = function () {
    ···
};

// es6
function UiComponent() {
    var button = document.getElementById('myButton');
    button.addEventListener('click', () => {
        console.log('CLICK');
        this.handleClick(); // (A)
    });
}

```

箭头函数适用于简单的返回一个值或者表达式的函数， 当参数只有一个的时候可以去掉括号：

```js
const arr = [1, 2, 3];
const squares = arr.map(x => x * x);

```

+ 返回多个函数值，在es5中，我们需要使用数组下标或者对象的key获取多个函数值，在es6中，我们可以用解构赋值的特性获取：

```js
// array, es5
var matchObj =
    /^(\d\d\d\d)-(\d\d)-(\d\d)$/
    .exec('2999-12-31');
var year = matchObj[1];
var month = matchObj[2];
var day = matchObj[3];

// array es6
const [, year, month, day] =
    /^(\d\d\d\d)-(\d\d)-(\d\d)$/
    .exec('2999-12-31');


// object es5
var obj = { foo: 123 };

var propDesc = Object.getOwnPropertyDescriptor(obj, 'foo');
var writable = propDesc.writable;
var configurable = propDesc.configurable;

// object es6
const obj = { foo: 123 };

const {writable, configurable} =
    Object.getOwnPropertyDescriptor(obj, 'foo');

console.log(writable, configurable); // true true


```

+ 用for of 代替 for 和forEach

在js中迭代是一件很麻烦的事情，在es5中，有两种选择，for和forEach，而在es6，for of具备了两种方法的优点：i

```js
// for loop in es5
var arr = ['a', 'b', 'c'];
for (var i=0; i<arr.length; i++) {
    var elem = arr[i];
    console.log(elem);
}

// forEach loop in es5
arr.forEach(function (elem) {
    console.log(elem);
});

// for of loop in es6
for (const [index, elem] of arr.entries()) {
    console.log(index+'. '+elem);
}

```

+ 处理函数默认参数
```js
// es5
function foo(x, y) {
    x = x || 0;
    y = y || 0;
    ···
}

// es6
function foo(x = 0, y = 0) {
    ...
}
```

+ 处理命名参数

```js
// es5
function selectEntries(options) {
    var start = options.start || 0;
    var end = options.end || -1;
    var step = options.step || 1;
    ···
}

// es6

function selectEntries({ start=0, end=-1, step=1 }) {
    ···
}
```

+ 处理可选参数

```js
// es5
function selectEntries(options) {
    options = options || {}; // (A)
    var start = options.start || 0;
    var end = options.end || -1;
    var step = options.step || 1;
    ···
}
// es6
function selectEntries({ start=0, end=-1, step=1 } = {}) {
    ···
}
```

+ 处理剩余参数

```js
// es5
function logAllArguments() {
    for (var i=0; i < arguments.length; i++) {
        console.log(arguments[i]);
    }
}

// es6
function logAllArguments(...args) {
    for (const arg of args) {
        console.log(arg);
    }
}

```

+ 将数组转换成参数

在es5中，我们用apply函数将数组变成参数(array -> parameters), 在es6中，我们用 __...__
```js
// es5 for example: Math.max receive arbitrary numbers and return the biggest one, but not for array
Math.max.apply(Math, [-1, 5, 11, 3])

var arr1 = ['a', 'b'];
var arr2 = ['c', 'd'];

arr1.push.apply(arr1, arr2);
    // arr1 is now ['a', 'b', 'c', 'd']

// es6
Math.max(...[-1, 5, 11, 3])

const arr1 = ['a', 'b'];
const arr2 = ['c', 'd'];

arr1.push(...arr2);
    // arr1 is now ['a', 'b', 'c', 'd']
```


+ From function expressions in object literals to method definitions 


```js 

var obj = {
    foo: function () {
        ···
    },
    bar: function () {
        this.foo();
    }, // trailing comma is legal in ES5
}

// es6
const obj = {
    foo() {
        ···
    },
    bar() {
        this.foo();
    },
}

```
+ 基类与类的继承

```js
// es5
function Person(name) {
    this.name = name;
}
Person.prototype.describe = function () {
    return 'Person called '+this.name;
};

// es6
class Person {
    constructor(name) {
        this.name = name;
    }
    describe() {
        return 'Person called '+this.name;
    }
}

// es5
function Employee(name, title) {
    Person.call(this, name); // super(name)
    this.title = title;
}
Employee.prototype = Object.create(Person.prototype);
Employee.prototype.constructor = Employee;
Employee.prototype.describe = function () {
    return Person.prototype.describe.call(this) // super.describe()
           + ' (' + this.title + ')';
};

class Employee extends Person {
    constructor(name, title) {
        super(name);
        this.title = title;
    }
    describe() {
        return super.describe() + ' (' + this.title + ')';
    }
}
```

+ map

很难想象一个使用了这么长时间的版本es5竟然没有map，用object代替知识一个权宜之计，所以在es6中，我们可以用map来使用实现map的功能了。（听起来很奇怪）

```js
var dict = Object.create(null);
function countWords(word) {
    var escapedWord = escapeKey(word);
    if (escapedWord in dict) {
        dict[escapedWord]++;
    } else {
        dict[escapedWord] = 1;
    }
}
function escapeKey(key) {
    if (key.indexOf('__proto__') === 0) {
        return key+'%';
    } else {
        return key;
    }
}

const map = new Map();
function countWords(word) {
    const count = map.get(word) || 0;
    map.set(word, count + 1);
}

```

+ string的新方法

四个新的方法：startsWith, endsWith, includes, repeat

```js
if (str.indexOf('x') === 0) {} // ES5
if (str.startsWith('x')) {} // ES6

function endsWith(str, suffix) { // ES5
  var index = str.indexOf(suffix);
  return index >= 0
    && index === str.length-suffix.length;
}
str.endsWith(suffix); // ES6

if (str.indexOf('x') >= 0) {} // ES5
if (str.includes('x')) {} // ES6

new Array(3+1).join('#') // ES5
'#'.repeat(3) // ES6

```

+ array的新方法

indexOf => findIndex: 可以识别NaN,
slice => from: 

```js
var arr1 = Array.prototype.slice.call(arguments); // ES5
const arr2 = Array.from(arguments); // ES6

```

+ fill方法来创建任意长度的具有相同值的数组：

```js
// ES5
var arr3 = Array.apply(null, new Array(2))
    .map(function (x) { return 'x' });
    // ['x', 'x']

// ES6
const arr4 = new Array(2).fill('x');
    // ['x', 'x']
```

+ 模块

