[TOC]
## 对象代理, 拦截器

```
Proxy的构造函数接受两个参数，第一个参数是所要代理的目标对象；第二个参数是一个配置对象，对于每一个被代理的操作，需要提供一个对应的处理函数，该函数将拦截对应的操作。
```
### 配置对象的所有选项
#### get(target,key,receiver)
拦截对象属性的读取，target是被代理的对象,key是需要拦截的键，receiver是一个可选的参数。
```javascript
let obj = { name: "zhangsan" };
let proxy = new Proxy(obj, {
  get: function(target, key) {
    console.log(target, key); // {name: "zhangsan"}
    return target[key];
  }
});
console.log(proxy.name); // zhangsan

```
#### set(target,key,value,receiver)
拦截对象属性的设置，target是被代理的对象,key是需要拦截的键，value是要设置的值，receiver是一个可选的参数。
```javascript
let obj = { name: "zhangsan" };
let proxy = new Proxy(obj, {
  set: function(target, key, value) {
    return target[key] = value;
  }
});
proxy.name = "lisi";
console.log(obj.name); // lisi

```
#### has(target, key)
拦截key in target的操作，返回一个布尔值。
```javascript
let obj = { name: "zhangsan" };
let proxy = new Proxy(obj, {
  has: function(target, key) {
    console.log(target, key);
    return key in target;
  }
});
console.log("name" in proxy); // true

```
#### deleteProperty(target,key)
拦截 delete target[key]的操作，返回一个布尔值。目标对象自身的不可配置的属性不能被deleteProperty方法删除，否则会报错。
```javascript
let obj = { name: "zhangsan" };
let proxy = new Proxy(obj, {
  deleteProperty: function(target, key) {
    console.log(target, key);
    if (!(key in target)) {
      throw TypeError("不存在的key");
    }
    return delete [target, key];
  }
});
console.log(delete proxy['name']);

```
#### ownKeys(target) 
返回一个数组，该方法返回目标对象所以自身属性的属性名。
```javascript
let obj = { name: "zhangsan" };
let proxy = new Proxy(obj, {
  ownKeys: function(target) {
    console.log(target);
    return ["name"];
  }
});
console.log(Object.keys(proxy));

```
#### getOwnPropertyDescriptor(target,key) 
返回属性的描述对象。
```javascript
let obj = { name: "zhangsan" };
let proxy = new Proxy(obj, {
  getOwnPropertyDescriptor: function(target, key) {
    console.log(target, key);
    return Object.getOwnPropertyDescriptor(target, key);
  }
});
console.log(Object.getOwnPropertyDescriptor(obj, "name"));

```
#### defineProperty(target,key,propDesc) 
返回一个布尔值。
```javascript
let obj = { name: "zhangsan" };
let proxy = new Proxy(obj, {
  defineProperty: function(target, key, desc) {
    console.log(target, key, desc);
    return true;
  }
});
console.log(Object.defineProperty(proxy, "name", {}));

```
#### preventExtensions(target) 
返回一个布尔值。
```javascript
let obj = { name: "zhansan" };
let proxy = new Proxy(obj, {
  preventExtensions: function(target) {
    console.log(target);
    Object.preventExtensions(target);
    return true;
  }
});
console.log(Object.preventExtensions(proxy));

```
#### getPrototypeOf(target) 
返回一个对象。
```javascript
let obj = { name: "zhangsan" };
let proxy = new Proxy(obj, {
  getPrototypeOf: function(target) {
    console.log(target);
    return obj;
  }
});
console.log(Object.getPrototypeOf(proxy));

```
#### isExtensible(target)
返回一个布尔值。
```javascript
let obj = { name: "zhangsan" };
let proxy = new Proxy(obj, {
  isExtensible: function(target) {
    console.log(target);
    return true;
  }
});
console.log(Object.isExtensible(proxy));

```
#### setPrototypeOf(target,proto)
返回一个布尔值。
```javascript
let obj = { name: "zhangsan" };
let proxy = new Proxy(obj, {
  setPrototypeOf: function(target, proto) {
    console.log(target, proto);
    target.prototype = proto;
    return true;
  }
});
console.log(Object.setPrototypeOf(proxy, obj));

```
#### apply(target,object,args)
```javascript
let proxy = new Proxy(Person, {
  apply: function(target, ctx, args) {
    console.log(target, ctx, args);
    return "i am a boy";
  }
});

function Person() {
  return "i am a gril";
}

console.log(proxy());

```
#### construct(target,args)
拦截Proxy实例作为构造函数调用的操作，比如new proxy(...args)。
```javascript
let proxy = new Proxy(Person, {
  construct: function(target, args) {
    console.log(target, args);
    return { value: "18" };
  }
});

function Person() {
  return "i am a gril";
}

console.log(new proxy().value);

```
### 示例：
类似实现类中的私有属性，外部不能访问。
```javascript
var obj = {
  time: '2018-07-01',
  name: 'denson',
  _r: 123
};
// new Proxy(target, handler); target表示被代理的对象，handler是个对象，包含get,set,has,deleteProperty,ownKeys，apply等方法
let monitor = new Proxy(obj, {
  //拦截对象属性的读取
  get(target, key) { //target表示拦截的obj对象,key表示monitor要读取的属性
    return target[key].replace('2018', '2019')
  },
  //拦截对象属性的设置
  set(target, key, value) {
    if (key == 'name') { //只允许修改name属性，其他属性不允许修改
      return target[key] = value
    } else {
      return target[key]
    }
  },
  //拦截key in object操作
  has(target, key) {
    if (key == 'name') {
      return target[key]
    } else {
      return false
    }
  },
  //拦截删除
  deleteProperty(target, key) {
    if (key.indexOf('_') > -1) { //删除以下划线开头的属性
      delete target[key]
    } else {
      return target[key]
    }
  },
  //拦截object.keys,object.getOwnPropertyNames,object.getOwnPropertySymbols
  ownKeys(target) { //保护time属性不被获取到
    return Object.keys(target).filter(item => item != 'time')
  }
})

//读取
console.log('get', monitor.time)
//设置
monitor.time = '2017'
monitor.name = 'test'
console.log('set', monitor.time, monitor)
//has
console.log('has', 'name' in monitor, 'time' in monitor)
//删除
delete monitor.name
console.log('delete', monitor)
delete monitor._r
console.log('delete', monitor)
//ownKeys
console.log('ownKeys', Object.keys(monitor))

```

示例
```javascript
// ES3,ES5 数据保护
{
  var Person = function () {
    var data = {
      name: 'es3',
      sex: 'male',
      age: 15
    }
    this.get = function (key) {
      return data[key]
    }
    this.set = function (key, value) {
      if (key !== 'sex') {
        data[key] = value
      }
    }
  }

  // 声明一个实例
  var person = new Person();
  // 读取
  console.table({name: person.get('name'), sex: person.get('sex'), age: person.get('age')});
  // 修改
  person.set('name', 'es3-cname');
  console.table({name: person.get('name'), sex: person.get('sex'), age: person.get('age')});
  person.set('sex', 'female');
  console.table({name: person.get('name'), sex: person.get('sex'), age: person.get('age')});
}


//ES5写法

{
  var Person = {
    name: 'es5',
    age: 15
  };

  Object.defineProperty(Person, 'sex', {
    writable: false,
    value: 'male'
  });

  console.table({name: Person.name, age: Person.age, sex: Person.sex});
  Person.name = 'es5-cname';
  console.table({name: Person.name, age: Person.age, sex: Person.sex});
  try {
    Person.sex = 'female';
    console.table({name: Person.name, age: Person.age, sex: Person.sex});
  } catch (e) {
    console.log(e);
  }
}

// ES6 写法
{
  let Person = {
    name: 'es6',
    sex: 'male',
    age: 15
  };

  let person = new Proxy(Person, {
    get(target, key) {
      return target[key]
    },
    set(target, key, value) {
      if (key !== 'sex') {
        target[key] = value;
      }
    }
  });

  console.table({
    name: person.name,
    sex: person.sex,
    age: person.age
  });

  try {
    person.sex = 'female';
  } catch (e) {
    console.log(e);
  } finally {

  }

}

```
对方法进行拦截示例
```javascript
function sum(a, b) {
  return a + b;
}

let newSum = new Proxy(sum, {
  apply(target, context, args) {
    // console.log(target, context, args);
    // console.log(arguments); // const [target, context, args] = arguments
    // 反射，通过Reflect对象直接拿到语言内部的东西
    // 例如 assign' in  Object --> Reflect.has(Object, 'assign') // true
    // return Reflect.apply(...arguments); // 5
    // Reflect.apply(调用的函数, this指向, 参数数组)， 类似与call()和apply()
    return Reflect.apply(...arguments) * 2; // 5 * 2 = 10
  }
})

console.log(newSum(2, 3))

```
#### 升序降序重置列表
```javascript
const list = [3, 2, 23, 1];
// 不允许修改list
Object.freeze(list);
const proxy = new Proxy(
  {},
  {
    get(target, key) {
      console.log(target, key);
      if (key === 'up') {
        return [].concat(list).sort((a, b) => a - b);
      } else if (key === ' down') {
        return [].concat(list).sort((a, b) => b - a);
      } else {
        return list;
      }
    },
    set() {
      return false;
    },
  }
);
console.log(proxy.default); // [ 3, 2, 23, 1 ]
console.log(proxy.up); // [ 1, 2, 3, 23 ]
console.log(proxy.down); // [ 3, 2, 23, 1 ]

```

