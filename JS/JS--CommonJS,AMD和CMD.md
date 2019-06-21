# 前端模块化
## CommonJS规范
CommonJS是JS进行模块化开发的一种规范，主要针对的是服务端开发，比如node.js就是遵循这种规范的实现。
它的`核心思想`就是：`每个文件就是一个模块`，有自己的作用域。在一个文件里面定义的变量、函数、类，都是私有的，对其他文件不可见。在文件内通过require方法来同步加载要依赖的模块，然后通过extports或则module.exports来导出需要暴露的接口。
**优点：**
1.所有代码都运行在模块作用域，不会污染全局作用域。
2.模块重用，可以多次加载，但是只会在第一次加载时运行一次，然后运行结果就被缓存了，以后再加载，就直接读取缓存结果。要想让模块再次运行，必须清除缓存。
3.模块加载的顺序，按照其在代码中出现的顺序。
**缺点：**
CommonJS规范加载模块是`同步的`，也就是说，只有加载完成，才能执行后面的操作。也就是说当它要用到某个模块时，是现加载现用，所以这样的流程就会造成加载速度慢，而且也会导致性能、可用性、调试和跨域访问等问题。

### Node.js为什么采用CommonJS规范?
由于Node.js主要用于服务器编程，模块文件一般都已经存在于本地硬盘，所以加载起来比较快，不用考虑非同步加载的方式，所以CommonJS规范比较适用。但是，如果是浏览器环境，要从服务器端加载模块，这时就必须采用非同步模式，因此浏览器端一般采用AMD规范。

## AMD
有了服务器端模块以后，很自然地，大家就想要客户端模块。而且最好两者能够兼容，一个模块不用修改，在服务器和浏览器都可以运行。
但是，由于CommonJS规范是同步加载，使得CommonJS规范不适用于浏览器环境，因为同步加载的方式，对服务器端不是一个问题，因为所有的模块都存放在本地硬盘，可以同步加载完成，等待时间就是硬盘的读取时间。但是，对于浏览器，这却是一个大问题，因为模块都放在服务器端，等待时间取决于网速的快慢，可能要等很长时间，浏览器处于”假死”状态。 因此，浏览器端的模块，不能采用”同步加载”（synchronous），只能采用”异步加载”（asynchronous），这就是AMD规范诞生的背景。

`AMD`是”Asynchronous Module Definition”的缩写，意思就是`异步模块定义”`。它采用异步方式加载模块，模块的加载不影响它后面语句的运行。所有依赖这个模块的语句，都定义在一个回调函数中，等到加载完成之后，这个回调函数才会运行。

`核心接口`
```
// 模块必须采用特定的define()函数来定义。
define(id ?, dependencies ?, factory)
// id:字符串，模块名称(可选)
// dependencies: 是我们要载入的依赖模块(可选)，使用相对路径。,注意是数组格式
// factory: 工厂方法，返回一个模块函数
// 如果一个模块不依赖其他模块，那么可以直接定义在define()函数之中。
// math.js
define(function () {
  var add = function (x, y) {
    return x + y;
  };
  return {
    add: add
  };
});
// 如果这个模块要依赖其他模块，那么define()函数的第一个参数，必须是一个数组，指明该模块的依赖性。
define(['Lib'], function (Lib) {
  function foo() {
    Lib.doSomething();
  }
  return {
    foo: foo
  };
});
// 当require()函数加载上面这个模块的时候，就会先加载Lib.js文件。
```
AMD也采用require()语句加载模块，但是不同于CommonJS，它要求两个参数：
```
require([module], callback);
// 第一个参数[module]，是一个数组，里面的成员就是要加载的模块；第二个参数callback，则是加载成功之后的回调函数。
// 例如
require(['math'], function (math) {
  math.add(2, 3);
});

// 主要有两个Javascript库实现了AMD规范：require.js和curl.js。
```

#### 优点
加载快速，尤其遇到多个大文件，因为并行解析，所以同一时间可以解析多个文件。

#### 缺点
并行加载，异步处理，加载顺序不一定，可能会造成一些困扰，甚至为程序埋下大坑。

## CMD
`CMD` (Common Module Definition)是另一种JS模块化方案，它与AMD很类似，`不同点`在于：AMD 推崇`依赖前置、提前执行`，CMD推崇`依赖就近、延迟执行`，用的时候再require。
```
/** AMD写法 **/
define(["a", "b", "c", "d", "e", "f"], function (a, b, c, d, e, f) {
  // 等于在最前面声明并初始化了要用到的所有模块
  a.doSomething();
  if (false) {
    // 即便没用到某个模块 b，但 b 还是提前执行了
    b.doSomething()
  }
});

/** CMD写法 **/
define(function (require, exports, module) {
  var a = require('./a'); //在需要时申明
  a.doSomething();
  if (false) {
    var b = require('./b');
    b.doSomething();
  }
});

/** sea.js **/
// 定义模块 math.js
define(function (require, exports, module) {
  var $ = require('jquery.js');
  var add = function (a, b) {
    return a + b;
  }
  exports.add = add;
});
// 加载模块
seajs.use(['math.js'], function (math) {
  var sum = math.add(1 + 2);
});
```

#### 优点
因为只有在使用的时候才会解析执行js文件，因此，每个JS文件的执行顺序在代码中是有体现的，是可控的。
####缺点
执行等待时间会叠加。因为每个文件执行时是同步执行（串行执行），因此时间是所有文件解析执行时间之和，尤其在文件较多较大时，这种缺点尤为明显。


## AMD/CMD区别
`AMD`是`预加载`，推崇`依赖前置、提前执行`。`CMD`是`懒加载`，推崇`依赖就近、延迟执行`。

(代码已格式化)

