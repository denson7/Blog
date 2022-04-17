[TOC]
[https://zhongsp.gitbooks.io/typescript-handbook/content/](https://zhongsp.gitbooks.io/typescript-handbook/content/)
## 函数中的泛型
```javascript
// 函数中的泛型,<T> 好比一个占位符，可以用来约束参数，返回值
function join1<T>(param1: T, param2: T) {
  return `${param1}${param2}`;
}

join1<string>("111", "222");
join1<number>(1, 2);

function map1<T>(params: T[]) {}
// 等价于
function map2<T>(params: Array<T>) {}

map1<string>(["hello"]);
map2<string>(["hello"]);

// 定义 param1 的类型为T, param2的类型为P
function join2<T, P>(param1: T, param2: P) {
  return `${param1}${param2}`;
}

join2<string, number>("111", 333);


interface Lengthwith {
  length: number
}
// 为泛型函数定义约束,比如必须含有length属性
function test <T extends Lengthwith>(arg: T): T{
  console.log(arg.length);
  return arg;
}
test('abc' );

function identity<T>(arg: T): T {
  return arg;
}

// 泛型函数
interface Hello<T> {
  (arg: T): T
}
let my1: Hello<string> = identity;
console.log(my1('hello'));

function getProperty<T, K extends keyof T>(obj: T, key: K) {
  return obj[key];
}
const x = {a: 1, b: 2};
getProperty(x, 'a'); // 1
// getProperty(x, 'm'); // key为m时不在对象x中, 保错

// 传入一个类的实例类型, 工厂函数
function create<T>(c: new() => T): T {
  return new c();
}
```
```javascript
type NullType<T> = T extends null | undefined ? never : T;

let demo1: NullType<number>
let demo2: NullType<null>
```
## 类中的泛型
```javascript
// 类中的泛型
// 普通的泛型
class DataManager1<T> {
  constructor(private data: T[]) {
    //
  }

  getItem(index: number): T {
    return this.data[index];
  }
}

const data1 = new DataManager1<string>(["hello"]);
const data12 = new DataManager1<number>([123]);
data1.getItem(0);

// 泛型约束
interface Item {
  name: string;
}
// 例1，要求：泛型必须包含name属性，解决方法就是使用泛型继承接口
class DataManager2<T extends Item> {
  constructor(private data: T[]) {
    //
  }

  getItem(index: number): string {
    return this.data[index].name; // 返回name的类型
  }
}

const data2 = new DataManager2([{ name: "lily" }]);
data2.getItem(0);

// 例2，要求：泛型的类型只能是number或string类型
class DataManager3<T extends number | string> {
  constructor(private data: T[]) {
    //
  }

  getItem(index: number): T {
    return this.data[index];
  }
}
```
## keyof 语法使用
```javascript
interface Person {
  name: string;
  age: number;
  isStudent: boolean;
}
class Teacher {
  constructor(private info: Person) {}
  // 根据传入的key来确定返回值的类型，使用 keyof 语法
  // T extends "name" 等价于 type T = "name"
  // T extends "age" 等价于 type T = "age"
  // T extends "isStudent" 等价于 type T = "isStudent"
  getInfo<T extends keyof Person>(key: T): Person[T] {
    return this.info[key];
  }
}

const t1 = new Teacher({
  name: "Sohpie",
  age: 20,
  isStudent: true,
});

const _name = t1.getInfo("name"); // string 类型
const _age = t1.getInfo("age"); // number类型
const _isStu = t1.getInfo("isStudent"); // boolean类型
```
```javascript
type KeyAny = keyof any
//  string | number | symbol
let t1: KeyAny = 'hello'
```

