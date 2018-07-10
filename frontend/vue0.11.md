vue 源码0.11.0 阅读笔记

vuejs是尤大出品的前端框架，相比于react和angular来说，背后没有大公司的支持，而是氛围良好的开发者社区。前不久，vue的star数目超过了react，虽然npm下载量还是react暂时领先，但是考虑到有一部分国人是用cnpm，我们大概认为vue的实力可以和react并驾齐驱了。

此系列笔记是我在阅读vue
0.11版本的源码时做的记录。为什么选择0.11呢？因为vue已经经过了三年左右的开发，现在的代码已经变得很复杂，而0.11是现在官网还有文档的一个比较早的版本，因此我选择先看这个版本的源码，以此来对vue的原理有更深的理解，

在阅读源码之前，我们先来简要回顾一下vuejs的一些基本概念，下面的内容实际上是对文档的整理:

本质上，vue是对mvvm模式的一种实现，主要分为ViewModel，Vie，Model三个部分，ViewModel是连接view（显示层，也就是我们看得到的ui部分）和Model（模型层，在ui背后的数据）的中间层，通过数据绑定来实现数据和ui的同步更新。而dom操作和数据显示分别通过指令和过滤器实现。

vue的核心是`Vue`构造器，我们一般通过

```js
var vm = new Vue({ /* options */ })
```
来创建一个vue实例。

```js

```
src文件目录：

├── api
│   ├── child.js
│   ├── data.js
│   ├── dom.js
│   ├── events.js
│   ├── global.js
│   └── lifecycle.js
├── batcher.js
├── binding.js
├── cache.js
├── compile
│   ├── compile.js
│   └── transclude.js
├── config.js
├── directive.js
├── directives
│   ├── attr.js
│   ├── class.js
│   ├── cloak.js
│   ├── component.js
│   ├── el.js
│   ├── html.js
│   ├── if.js
│   ├── index.js
│   ├── model
│   │   ├── checkbox.js
│   │   ├── default.js
│   │   ├── index.js
│   │   ├── radio.js
│   │   └── select.js
│   ├── on.js
│   ├── partial.js
│   ├── ref.js
│   ├── repeat.js
│   ├── show.js
│   ├── style.js
│   ├── text.js
│   ├── transition.js
│   └── with.js
├── filters
│   ├── array-filters.js
│   └── index.js
├── instance
│   ├── compile.js
│   ├── events.js
│   ├── init.js
│   └── scope.js
├── observer
│   ├── array.js
│   ├── index.js
│   └── object.js
├── parse
│   ├── directive.js
│   ├── expression.js
│   ├── path.js
│   ├── template.js
│   └── text.js
├── transition
│   ├── css.js
│   ├── index.js
│   └── js.js
├── util
│   ├── debug.js
│   ├── dom.js
│   ├── env.js
│   ├── filter.js
│   ├── index.js
│   ├── lang.js
│   └── merge-option.js
├── vue.js
└── watcher.js

为了讨论方便，我们会经常贴出不带块注释的源代码。

程序入口是从vue.js开始的，代码不是很多，直接贴到这里来看：

```javascript
var _ = require('./util')
var extend = _.extend


function Vue (options) {
  this._init(options)
}


extend(Vue, require('./api/global'))


Vue.options = {
  directives  : require('./directives'),
  filters     : require('./filters'),
  partials    : {},
  transitions : {},
  components  : {}
}


var p = Vue.prototype


Object.defineProperty(p, '$data', {
  get: function () {
    return this._data
  },
  set: function (newData) {
    this._setData(newData)
  }
})


extend(p, require('./instance/init'))
extend(p, require('./instance/events'))
extend(p, require('./instance/scope'))
extend(p, require('./instance/compile'))

extend(p, require('./api/data'))
extend(p, require('./api/dom'))
extend(p, require('./api/events'))
extend(p, require('./api/child'))
extend(p, require('./api/lifecycle'))

module.exports = _.Vue = Vue
```

首先引入工具类，这里主要用到函数extend,  即：

```javascript
exports.extend = function (to, from) {
  for (var key in from) {
    to[key] = from[key]
  }
}
```
也就是把`from`对象的属性都复制到`to`对象中。

接着定义函数Vue,
也就是构造器。用`options`参数初始化，`_init`函数是从`/instance/init`中而来，我们稍后看这个文件里面的代码。

然后是扩充Vue的prototype。添加了`./insatnce`和`./api`中为一些方法。

最后一句就是导出我们在hello world例子中用到的构造函数`Vue`。

接下来看构造器中提到的`_init`方法，在源码中的注释中可以看到，对于方法的命名的惯例是：

+ `$` 开头的是公开的方法或者属性
+ `_` 开头的是内部的方法或者属性
+ 没有前缀的方法是用来代理用户数据的

```javascript
var mergeOptions = require('../util/merge-option')

exports._init = function (options) {

  options = options || {}

  this.$el           = null
  this.$parent       = options._parent
  this.$root         = options._root || this
  this.$             = {} // child vm references
  this.$$            = {} // element references
  this._watcherList  = [] // all watchers as an array
  this._watchers     = {} // internal watchers as a hash
  this._userWatchers = {} // user watchers as a hash
  this._directives   = [] // all directives

  // a flag to avoid this being observed
  this._isVue = true

  // events bookkeeping
  this._events         = {}    // registered callbacks
  this._eventsCount    = {}    // for $broadcast optimization
  this._eventCancelled = false // for event cancellation

  // block instance properties
  this._isBlock     = false
  this._blockStart  =          // @type {CommentNode}
  this._blockEnd    = null     // @type {CommentNode}

  // lifecycle state
  this._isCompiled  =
  this._isDestroyed =
  this._isReady     =
  this._isAttached  =
  this._isBeingDestroyed = false

  // children
  this._children =         // @type {Array}
  this._childCtors = null  // @type {Object} - hash to cache
                           // child constructors

  // merge options.
  options = this.$options = mergeOptions(
    this.constructor.options,
    options,
    this
  )

  // set data after merge.
  this._data = options.data || {}

  // initialize data observation and scope inheritance.
  this._initScope()

  // setup event system and option events.
  this._initEvents()

  // call created hook
  this._callHook('created')

  // if `el` option is passed, start compilation.
  if (options.el) {
    this.$mount(options.el)
  }
}
```

第一行是引入工具函数`mergeOptions`，源码有点长这里就不全部贴出来了。在`merge-optins.js`中编写了很多`options`复写的策略，也就是针对不同类型对象怎么合并`parent`和`child`的option到最终的值的函数。

最后是`merge-options`函数的定义。该函数是Vue实例和继承中核心的工具函数。

```javascript
module.exports = function mergeOptions (parent, child, vm) {
  guardComponents(child.components)
  var options = {}
  var key
  for (key in parent) {
    merge(parent[key], child[key], key)
  }
  for (key in child) {
    if (!(parent.hasOwnProperty(key))) {
      merge(parent[key], child[key], key)
    }
  }
  var mixins = child.mixins
  if (mixins) {
    for (var i = 0, l = mixins.length; i < l; i++) {
      for (key in mixins[i]) {
        merge(options[key], mixins[i][key], key)
      }
    }
  }
  function merge (parentVal, childVal, key) {
    var strat = strats[key] || defaultStrat
    options[key] = strat(parentVal, childVal, vm, key)
  }
  return options
}
```

这里的parent, child,
vm都是`Vue`的实例。先看函数内部的一个函数`merge`，通过传入的key参数来获取对应的merge strategy，如果没有事先定义过的strategy，就用默认的strategy。而默认的strategy是:

```javascript
var defaultStrat = function (parentVal, childVal) {
  return childVal === undefined
    ? parentVal
    : childVal
}
```

假如传入的key是计算属后者方法，也就是`computed`或者`methods`，那么对应strategy是：

```javascript
strats.methods =
strats.computed = function (parentVal, childVal) {
  if (!childVal) return parentVal
  if (!parentVal) return childVal
  var ret = Object.create(parentVal)
  extend(ret, childVal)
  return ret
}
```

如果你写过vue代码的话，两个strategy的意思都很直观，这里就不解释了。

那么在回到`merge-options`函数，主要内容是对两个for循环，分别遍历parent中的每个key传入merge函数，也就是根据key找到对应的merge
strategy，merge即可，然后遍历child中parent没有的key，做同样的操作。然后对child中的mixin做同样的操作。

`merge-options`看完之后就回到`init.js`中的`_init`方法。前面看到，这个方法在`Vue`构造器中调用了，这意味着每个实例都会通过这个函数进行初始化，即便是通过扩展过的构造器创建的实例，所谓扩展过的构造器是指：

```javascript
extend(Vue, require('./api/global'))
```

即混入了global中的方法。这个具体后面再讲。

`init`方法中，首先定义了一些公有的属性和内部属性，分别是：

+ $el 表示挂载元素
+ $parent
+ $root
+ $
+ $$
+ `_watcherList` 将所有的watcher放在数组中
+ `_watchers` 内部watcher的map
+ `_userwatchers` 用户watcher的map
+ `_directives` 所有指令的数组

后面也是一些变量，尤大都写了注释，就不一一介绍了。

值得注意的是下面几行代码：

```javascript
this._isCompiled  =
this._isDestroyed =
this._isReady     =
this._isAttached  =
this._isBeingDestroyed = false
```

这代表了vue实例声明周期的各个阶段。

```javascript
// merge options.
options = this.$options = mergeOptions(
    this.constructor.options,
    options,
    this
)
```

也就是说最后的options对象是传入的options对象，构造器的options对象，以及自身的合并，有同学可能会奇怪构造器的options是哪里来的。之前在`vue.js`中其实出现过。

```javascript
Vue.options = {
  directives  : require('./directives'),
  filters     : require('./filters'),
  partials    : {},
  transitions : {},
  components  : {}
}
```

`init`函数的最后，判断如果给出了`el`参数，那么就调用`$mount`方法，即开始`complition过程`。那么自然，接下来就是分析编译过程。

`$mount`方法在`api/lifecycle.js`里面，这里贴出来去掉注释的代码：

```javascript

exports.$mount = function (el) {
  if (this._isCompiled) {
    _.warn('$mount() should be called only once.')
    return
  } // $mount函数只能被调用一次
  if (!el) {
    el = document.createElement('div')  // el没有值的话就创建一个div元素
  } else if (typeof el === 'string') {
    // 是字符串的情况
    var selector = el
    el = document.querySelector(el)
    if (!el) {
      _.warn('Cannot find element: ' + selector)
      return
    }
  }
  // 不是字符串的情况  调用_compile函数处理
  this._compile(el)
  this._isCompiled = true
  this._callHook('compiled')
  if (_.inDoc(this.$el)) {    // 判断这个元素是否已经在文档对象中
    this._callHook('attached') 
    this._initDOMHooks() 
    ready.call(this)
  } else {
    this._initDOMHooks()
    this.$once('hook:attached', ready)
  }
  return this
}


function ready () {
  this._isAttached = true
  this._isReady = true
  this._callHook('ready')
}

exports.$destroy = function (remove) {
  if (this._isBeingDestroyed) {
    return
  }
  this._callHook('beforeDestroy')
  this._isBeingDestroyed = true
  var i
  // remove self from parent. only necessary
  // if parent is not being destroyed as well.
  var parent = this.$parent
  if (parent && !parent._isBeingDestroyed) {
    i = parent._children.indexOf(this)
    parent._children.splice(i, 1)
  }
  // destroy all children.
  if (this._children) {
    i = this._children.length
    while (i--) {
      this._children[i].$destroy()
    }
  }
  // teardown all directives. this also tearsdown all
  // directive-owned watchers.
  i = this._directives.length
  while (i--) {
    this._directives[i]._teardown()
  }
  // teardown all user watchers.
  for (i in this._userWatchers) {
    this._userWatchers[i].teardown()
  }
  // remove reference to self on $el
  if (this.$el) {
    this.$el.__vue__ = null
  }
  // remove DOM element
  var self = this
  if (remove && this.$el) {
    this.$remove(function () {
      cleanup(self)
    })
  } else {
    cleanup(self)
  }
}


function cleanup (vm) {
  // remove reference from data ob
  vm._data.__ob__.removeVm(vm)
  vm._data =
  vm._watchers =
  vm._userWatchers =
  vm._watcherList =
  vm.$el =
  vm.$parent =
  vm.$root =
  vm._children =
  vm._bindings =
  vm._directives = null
  // call the last hook...
  vm._isDestroyed = true
  vm._callHook('destroyed')
  // turn off all instance listeners.
  vm.$off() 
}


exports.$compile = function (el) {
  return compile(el, this.$options, true)(this, el)
}
```

`$mount`函数是整个magic开始的地方，这里是考虑最普通的情况，传入了`el`参数，从这里是编译的开始，`el`可以是选择器字符串，html元素，或者DocumentFragment。

首先是引入了`compile/compile.js`，这里先不展开。

然后是`$mount`函数的函数体。


