[TOC]
## Reflect
Reflect是ES6新增的一个`全局对象` 它的特性和Proxy的用法非常类似   不用new一个实例， 直接用即可。

### Reflect包含的方法

```javascript
Reflect.set // 设置对象属性，返回布尔值
Reflect.get // 获取对象属性值
Reflect.has // 检查对象属性，返回布尔值
Reflect.deleteProperty // 删除对象属性，返回布尔值
Reflect.ownKeys // 遍历对象属性，返回数字
Reflect.apply // 绑定this后执行指定函数
Reflect.construct // 调用构造函数创建实例
Reflect.defineProperty // 定义对象属性，返回布尔值
Reflect.getOwnPropertyDescriptor // 返回对象属性描述，返回对象
Reflect.getPrototypeOf // 返回对象原型
Reflect.isExtensible // 返回对象是否可扩展，返回布尔值
Reflect.preventExtensions // 设置对象不可扩展，返回布尔值
Reflect.setPrototypeOf // 设置对象原型

```
示例
```javascript
var obj = {
  time: '2018-07-01',
  name: 'denson',
  _r: 123
};

//直接使用Reflect，不需要new
console.log('reflect get:', Reflect.get(obj, 'time'))
//设置
Reflect.set(obj, 'name', 'demo')
console.log(obj)
//has
console.log('reflect has:', Reflect.has(obj, 'name'))

```


