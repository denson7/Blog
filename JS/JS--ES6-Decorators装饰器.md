[TOC]
## Decorators 装饰器
`装饰器`：是一种特殊类型的声明，它能够被附加到类，方法，属性或参数上，可以用来修改类的行为。简单的来说，`装饰器就是一个方法`，可以注入到类，方法，属性或参数上，用来`扩展`其功能。

### 安装插件
```
npm i --save-dev babel-plugin-transform-decorators-legacy
```
### 配置
在项目目录下的.babelrc 文件（babel配置文件）中，添加如下内容：
```
{
    "plugins": ["transform-decorators-legacy"]
}
```
### 第三方库 core-decorators
```
//查看它的文档
https://github.com/jayphelps/core-decorators
```
### 写法
```
1.普通装饰器写法(默认参数)
2.装饰器工厂写法(可接收自定义参数)
```
## 分类
装饰器可以根据装饰的对象不同分为：`类装饰器`，`属性装饰器`，`方法装饰器`，`参数装饰器`等。
### 类装饰器
类装饰器在类声明之前被声明(紧靠着类声明)，类装饰器应用于类构造函数，可以用来监视，修改或替换类定义。

```javascript
// 装饰器函数的第一个参数，就是所要装饰的目标类
function log(target){ // 即这个 target 在这里就是 MyClass 这个类
  // 扩展属性
  target.status = "动态扩展的属性";
  // 扩展方法
  target.prototype.logger = () => `动态扩展的logger方法`
}

// 类装饰器, 用来修饰一个类
// 这里的log就是为MyClass类添加一个logger方法和添加一个status属性
@log
class MyClass{}
const test = new MyClass();
console.log(MyClass.status); // 动态扩展的属性
console.log(test.logger()); // 动态扩展的logger方法

// 总结
@decorator
class A {}
// 等同于
class A {}
A = decorator(A) || A;

```
```javascript
// 类的装饰器
function testDecorator1(constructor: any) {
  constructor.prototype.getName = () => {
    console.log("111");
  };
}

// 工厂函数的包装,对装饰器进行一些判断
function testDecorator2(flag: boolean) {
  // 根据条件来判断是否需要装饰器
  if (flag) {
    return function (constructor: any) {
      constructor.prototype.getName = () => {
        console.log("111");
      };
    };
  } else {
    return function (constructor: any) {};
  }
}

@testDecorator1
// @testDecorator2(true)
class Test {}

const test1 = new Test();
// console.log((test1 as any).getName());

// (...args: any[]) => any 表示一个函数, 参数是任意类型,返回时任意类型
// new (...args: any[]) => any 表示一个构造函数
function testDecorator3<T extends new (...args: any[]) => any>(constructor: T) {
  return class extends constructor {
    // 对原来的构造函数做一些扩展
    name = "world";
    getName = () => {
      return this.name;
    };
  };
}
@testDecorator3
class Test3 {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
}

const test3 = new Test3("hello");
console.log((test3 as any).getName());

// 工厂函数形式对原来的类做装饰器修饰,生成新的类
function testDecorator4() {
  return function <T extends new (...args: any[]) => any>(constructor: T) {
    return class extends constructor {
      // 对原来的构造函数做一些扩展
      name = "tomorrow";
      getName = () => {
        return this.name;
      };
    };
  };
}

const Test4 = testDecorator4()(
  class {
    name: string;
    constructor(name: string) {
      this.name = name;
    }
  }
);
const test4 = new Test4("nihao");
console.log(test4.getName());

```
### 带参数的装饰器
```javascript
// 由于装饰器第一个参数是目标类，所以如果要传递参数到装饰器中，就可以在装饰器外面再封装一层函数
// 即这里的装饰器 testDemo 就可以接收一个参数，这就可以修改装饰器的行为。
// 注意：装饰器对类的行为的改变，是代码编译时发生的，而不是在运行时。也就是说装饰器本质就是编译时执行的函数
function testDemo(isDesc) {
  return function (target, name, descriptor) {
    // 为目标类Demo添加一个静态属性isDesc
    target.isDesc = isDesc;
  }
}

// 带参数的装饰器
@testDemo(false)
class Demo {}

console.log(Demo.isDesc);

// 如果想添加实例属性，可以通过目标类的 prototype 对象操作
function mixins(...list) {
  return function (target) {
    Object.assign(target.prototype, ...list);
  }
}

// 一个对象
const Foo = {
  foo() {
    console.log('foo');
  }
};

@mixins(Foo)
class MyClass1 {
}

let obj = new MyClass1();

obj.foo(); // foo

```
### 类方法装饰器

```javascript
// 类方法装饰器
// 装饰器函数 readonly 一共可以接受三个参数
// target 对于类的普通方法修饰的目标对象(类的原型对象)，对于类的静态方法，target是类的构造函数
// name 方法名(要装饰的属性名)
// descriptor对象值如下(属性的描述对象)
// {
//  value: specifiedFunction,
//  enumerable: false,
//  configurable: true,
//  writable: true
// };
function readonly(target, name, descriptor) {
  descriptor.writable = false;
  return descriptor;
}

class Person {
  constructor() {
    this.first = 'A';
    this.last = 'B';
  }

  @readonly
  name() {
    return `${this.first} ${this.last}`;
  }
}

let p = new Person();
console.log(p.name());

// 类方法装饰器
function log(target, name, descriptor) {
  let oldValue = descriptor.value; // descriptor.value 就是要被装饰的函数
  console.log(oldValue);

  descriptor.value = function () {
    console.log(`call ${name} with`, arguments);
    return oldValue.apply(this, arguments);
  };
  return descriptor;
}

class Math {
  @log
  add(a, b) {
    return a + b
  }
}

let m = new Math();
const res = m.add(3, 4);
console.log(res);

```
### 类属性装饰器

```
// 设置属性不能被更改
// function nameDecorator(target: any, key: string): any {
//   // 返回一个新的descriptor
//   const descriptor: PropertyDescriptor = {
//     writable: false
//   };
//   return descriptor;
// }

// test.name = 'zhangsan';

// 属性装饰器只能接收2个参数
function nameDecorator(target: any, key: string): any {
  // 修改的并不是实例上的 name， 而是原型上的 name
  target[key] = "hello";
}

// name 放在实例上
class Test {
  // 属性装饰器
  @nameDecorator
  name = "Demo";
}

const test = new Test();
console.log(test.name); // Demo
console.log((test as any).__proto__.name); // hello

```
### 函数参数装饰器
```javascript
// 第一个参数是原型，第二个参数是方法名，第三个参数是参数所在的位置
function paramDecorator(target: any, method: string, paramIndex: number) {
  console.log(target, method, paramIndex); // Test { getInfo: [Function] } getInfo 1
}

class Test {
  getInfo(name: string, @paramDecorator age: number) {
    console.log(name, age);
  }
}

const test = new Test();
test.getInfo("Hello", 30);

```
### 访问器(get/set)装饰器
```javascript
function visitDecorator(
  target: any,
  key: string,
  descriptor: PropertyDescriptor
) {
  descriptor.writable = true;
}

class Demo {
  private _name: string;
  constructor(name: string) {
    this._name = name;
  }
  get name() {
    return this._name;
  }
  @visitDecorator
  set name(name: string) {
    this._name = name;
  }
}

const demo1 = new Demo("hello");
// demo1.name = "demo";
console.log(demo1.name);

```
### 多个装饰器执行顺序
```javascript
// 同一个方法有多个修饰器，会像剥洋葱一样，先从外到内进入，然后由内向外执行
function test(id){
  console.log('input', id);
  return function (target, property, descriptor) {
    console.log('return', id);
  }
}
class Test {
  // 外层修饰器 @test(1) 先进入，但是内层修饰器 @test(2) 先执行
  @test(1)
  @test(2)
  method(){
    console.log('this is method')
  }
}

const e1 = new Test();
console.log(e1.method());
// 输出
// input 1
// input 2
// return 2
// return 1
// this is method

```
```javascript
// time => 计数and计时
const labels = {};
// Exported for mocking in tests
const defaultConsole = {
  time: console.time
    ? console.time.bind(console)
    : label => {
      labels[label] = new Date();
    },
  timeEnd: console.timeEnd
    ? console.timeEnd.bind(console)
    : label => {
      const timeNow = new Date();
      const timeTaken = timeNow - labels[label];
      delete labels[label];
      console.info(`${label}: ${timeTaken}ms`);
    }
};
let count = 0;

const time = (params = {prefix: null, console: defaultConsole}) => (
  target,
  prototypeKey,
  descriptor
) => {
  const fn = descriptor.value;
  let {prefix} = params;
  const {console} = params;
  if (prefix === null) {
    prefix = `${target.constructor.name}.${prototypeKey}`;
  }

  if (typeof fn !== "function") {
    throw new SyntaxError(`@time can only be used on functions, not: ${fn}`);
  }

  return {
    ...descriptor,
    async value(...args) {
      const label = `${prefix}-${count}`;
      count += 1;
      console.time(label);

      try {
        return await fn.apply(this, args);
      } finally {
        console.timeEnd(label);
      }
    }
  };
};

// deprecate => 标记废弃
const DEFAULT_MSG = "This function will be removed in future versions.";
const deprecate = (params = {options: {}}) => (
  target,
  prototypeKey,
  descriptor
) => {
  if (typeof descriptor.value !== "function") {
    throw new SyntaxError("Only functions can be marked as deprecated");
  }

  const methodSignature = `${target.constructor.name}#${prototypeKey}`;
  let {msg = DEFAULT_MSG} = params;
  const {options} = params;

  if (options.url) {
    msg += `\n\n    See ${options.url} for more details.\n\n`;
  }

  return {
    ...descriptor,
    value(...args) {
      console.warn(`DEPRECATION ${methodSignature}: ${msg}`);
      return descriptor.value.apply(this, args);
    }
  };
};

// test sequence 测试顺序
const testSequence1 = (params = {}) => (
  target,
  prototypeKey,
  descriptor
) => {
  const oldValue = descriptor.value;
  return {
    ...descriptor,
    value(...args) {
      console.log("test1");
      oldValue.apply(this, args);
    }
  };
};

const testSequence2 = (params = {}) => (
  target,
  prototypeKey,
  descriptor
) => {
  const oldValue = descriptor.value;
  return {
    ...descriptor,
    value(...args) {
      console.log("test2");
      oldValue.apply(this, args);
    }
  };
};


class ClassF {
  constructor() {
    this.result = {}
  }

  @time()
  @deprecate({ options: { url: 'https://github.com/xx' } })
  @testSequence1()
  @testSequence2()
  fun() {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve(this.result)
      }, 3000);
    });
  }
}
const classf = new ClassF();
classf.fun();
classf.fun();

```
#### 装饰器应用
示例1
```javascript
// 埋点，做日志统计
let log = (type) => {
  return function (target, name, descriptor) {
    let src_method = descriptor.value;
    descriptor.value = (...arg) => {
      src_method.apply(target, arg);
      console.info(`log${type}`); // 实现埋点，真实的业务中直接换成一个接口就可以了
    }
  }
};

class AD {

  @log('show')
  show() {
    console.log('adisshow');
  };

  @log('click')
  click() {
    console.log('adisclick');
  }
}

letad = new AD();

ad.show();
ad.click();

```
### 示例2
```javascript
const userInfo: any = undefined;

function catchError1(target: any, key: string, descriptor: PropertyDescriptor){
  const fn = descriptor.value;
  descriptor.value = function() {
    try {
      fn();
    } catch (e) {
      console.log("error");
    }
  };
}

// 将上面的catchError1装饰器通过工厂模式改造,外层包裹一层，然后返回一个函数即可
function catchError(msg: string) {
  return function(target: any, key: string, descriptor: PropertyDescriptor) {
    // 获取原来的值,原来的值是一个函数
    const fn = descriptor.value;
    // 修改原来的函数，添加try..catch
    descriptor.value = function() {
      try {
        fn();
      } catch (e) {
        console.log(msg);
      }
    };
  };
}

class Test {
  @catchError('userInfo.name 不存在')
  getName() {
    return userInfo.name;
  }
  @catchError('userInfo.age 不存在')
  getAge() {
    return userInfo.age;
  }
  @catchError('userInfo.gender 不存在')
  getGender() {
    return userInfo.gender;
  }
}

const test = new Test();
test.getName();
test.getAge();
```















