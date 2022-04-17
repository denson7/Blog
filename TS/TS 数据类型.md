[TOC]
TypeScript时JavaScript的超集，所以TS的数据类型包含所有的JS数据类型。
JavaScript的基础数据类型包含：`String`、`Number`、`Boolean`、`Object（Array、Function）`、`Symbol`、`undefined`、`null`。
TypeScript新增的数据类型：`void`、`any`、`never`、`元组`、`枚举`、`高级类型`。

## 基础数据类型
### 布尔类型 boolean
```javascript
// 布尔值：使用 boolean 定义布尔值类型
let isDone: boolean = false;
// 注意，使用构造函数 Boolean 创造的对象不是布尔值
// let createdByNewBoolean: boolean = new Boolean(1); // 错误写法
// 报错：Type 'Boolean' is not assignable to type 'boolean'
// 事实上 new Boolean() 返回的是一个 Boolean 对象
let createdByNewBoolean: Boolean = new Boolean(1); // 正确写法
// 直接调用 Boolean 返回的是 boolean 类型
let createdByBoolean: boolean = Boolean(1); // 正确写法

```
### 数值类型 number
```javascript
// 数值：使用 number 定义数值类型
let num: number = 6;
let hexNum: number = 0xf00d;
// ES6 二进制表示法
let binNum: number = 0b1010;
// ES6 八进制表示法
let obNum: number = 0o744;
let notANumber: number = NaN;
let infinityNumber: number = Infinity;

```
### 字符串类型 string
```javascript
// 字符串：使用 string 定义字符串类型
let myName: string = 'Tom';
let sentence: string = `Hello, my name is ${myName}`

```
### 数组类型
```javascript
// 数组类型
// 方法一，使用 类型[] 来定义
let nums1: number[] = [1, 1, 2, 3, 5];

// 方法二，使用 泛型 来定义
let nums2: Array<number | string> = [1, 1, 2, 3, 5, 'abc'];

// 方法三，使用 接口 来定义
interface NumberArray {
    [index: number]: number;
}
let nums3: NumberArray = [1, 1, 2, 3, 5];

// 方法四，使用 any 来定义，表示数组中允许任意类型
let nums4: any[] = ['Liu', 25, { height: '172' }];

```
### null 和undefined
```javascript
// Null 和 Undefined 在 TypeScript 中，可以使用 null 和 undefined 来定义这两个原始数据类型
let u: undefined = undefined;
let n: null = null;
// undefined 类型的变量只能被赋值为 undefined
// null 类型的变量只能被赋值为 null

// 注意：与 void 的区别是，undefined 和 null 是所有类型的子类型。
// 也就是说 undefined 类型的变量，可以赋值给 number, string等基本类型的变量
let un: undefined;
let num1: number = un;

```
### 函数类型
```javascript
// 函数类型
// 方法一，对于使用函数声明定义的函数
function sum(x:number, y:number) :number{
    return x + y;
}

// 方法二，对于使用函数表达式定义的函数
let mySum1 = function (x: number, y:number):number {
    return x + y;
};
// 或手动添加类型
let mySum2: (x: number, y: number) => number = function (x: number, y: number): number {
    return x + y;
};

// 方法三，使用接口方法定义函数
interface SearchFunc {
    (a: string, b: string): boolean;
}

let mySearch: SearchFunc;
mySearch = function(a: string, b: string) {
    return a.search(b) !== -1;
}
// 可选参数, 使用? 来修饰参数表示参数是否可选，注意，可选参数必须在必需参数后面
function getName1(firstName: string, lastName?: string) {
    return lastName ? (firstName + ' ' + lastName) : firstName
}
let name1 = getName1('Tom', 'Cat');
let name2 = getName1('Tom');

// 参数默认值
function getName2(firstName: string = 'John', lastName: string = 'Michael') {
    return firstName + ' ' + lastName;
}
let tc = getName2('Tom', 'Cat');
let t = getName2('Tom');

// 剩余参数， 事实上，items 是一个数组，所以可以使用数组的类型来定义
function push(array: any[], ...items: any[]) {
    items.forEach(function(item) {
        array.push(item);
    });
}
let a = [];
push(a, 1, 2, 3);

// 重载
// 函数重载就是允许一个函数接受不同数量或类型的参数时，作出不同的处理
// 例如反转字符串或数字，反转之后的数据类型保持不变
function reverse(x: number): number;
function reverse(x: string): string;
function reverse(x: number | string): number | string {
    if (typeof x === 'number') {
        return Number(x.toString().split('').reverse().join(''));
    } else if (typeof x === 'string') {
        return x.split('').reverse().join('');
    }
}

```
### 空值 void
```javascript
// 空值：使用 void 表示没有任何返回值的函数
function alertName(): void {
    console.log('My name is XXX');
}
// 注意，声明一个 void 类型的变量没有什么用，因为你只能将它赋值为 undefined 和 null
let unusable: void = undefined;

```
### 任意类型 any
```javascript
// 任意值：使用any 表示允许赋值为任意类型，换句话说放弃了类型检查
let myFavoriteNumber: any = 'name';
myFavoriteNumber = 7;

// 变量如果在声明的时候，未指定其类型，那么它会被识别为任意值类型
let something; // 没有声明类型，则会被认为是any类型, 等价于 let something: any;
something = 'hello'
something = 11
// 类型推断，声明了，但是有赋值的情况，会进行类型推断
let str = 'hello'; // 等价于 let str:string = 'hello'
str = 7; // 报错 

```
### 枚举 enum
```javascript

// 枚举:使用 enum 关键字定义枚举类型
enum Status {
  OFFLINE, // 默认从0开始
  ONLINE,
  DELETED,
}
console.log(Status);
/**
 {
  '0': 'OFFLINE',
  '1': 'ONLINE',
  '2': 'DELETED',
  OFFLINE: 0,
  ONLINE: 1,
  DELETED: 2
}
 */
enum Status2 {
  OFFLINE,
  ONLINE = 4,
  DELETED,
}
console.log(Status2);
/**
 {
  '0': 'OFFLINE',
  '4': 'ONLINE',
  '5': 'DELETED',
  OFFLINE: 0,
  ONLINE: 4,
  DELETED: 5
}
 */

```
```javascript
// 双向映射
const Direction {
  UP = 10,
  DOWN,
  LEFT,
  RIGHT,
}
console.log(Direction.UP); // 10
console.log(Direction[10]); // UP
```
### 元组类型
元组是一种特殊的数组类型，它限定了元素的类型和个数，元素的顺序和类型都不能为其他类型。
```javascript
let tuple: [number, string] = [123, 'adfs'];
```
## 高级类型
### Record 类型
Record 是一个常用的类型，它会将一个类型的所有属性都映射到另一个类型上，并创造一个新的类型。
```javascript
// Record
const record: Record<string, any> = {};


type animal = 'dog' | 'cat';

interface IInfo {
    name: string,
    age: number,
}

type IPets1 = Record<animal, IInfo>;

type IPets2 = Record<animal | 'bird', IInfo>;

const animalsInfo1: IPets1 = {
    dog: {
        name: 'dogName',
        age: 2
    },
    cat: {
        name: 'catName',
        age: 3
    }
};

const animalsInfo2: IPets2 = {
    dog: {
        name: 'dogName',
        age: 2
    },
    cat: {
        name: 'catName',
        age: 3
    },
    bird: {
        name: 'otherAnamialName',
        age: 10
    }
};

```
### 联合类型 |
```javascript
// 联合类型使用 | 分隔每个类型，取值可以是多种类型中的一种
let str: string | number;
str = 'demo'; // 正确
str = 7; // 正确
// str = true; // 报错



// 当 TS 不确定一个联合类型的变量到底是哪个类型的时候，只能访问此联合类型的所有类型里共有的属性或方法
// 错误写法
// function getLength(something: string | number): number {
//     return something.length; // 报错,length 不是string和number的共有属性，所以报错
// }

// 正确写法
function getString(something: string | number): string {
    return something.toString();
}

let a1: number | string = 1; // or '1'
let a2: 'a' | 'b' | 'c';
let b: 1 | 2 | 3;

interface DogInterface {
    run (): void
}
interface CatInterface {
    jump (): void
}
class Dog implements DogInterface {
    run () {}
    eat () {}
}
class Cat implements CatInterface {
    jump () {}
    eat () {}
}
enum Master { Boy, Girl }
function getPet (master: Master) {
    let pet = master === Master.Boy ? new Dog() : new Cat()
    pet.eat()
    return pet
}

```
### 交叉类型 &
```javascript
// 交叉类型
// 使用 & 符号，多个类型合并为一个类型，新的类型具有所有类型的特性。
interface DogInterface {
    run (): void;
}
interface CatInterface {
    jump (): void;
}
let pet: DogInterface & CatInterface = {
    run () {},
    jump () {}
}

```
### 索引类型
```javascript
let obj1 = {
    a: 1,
    b: 2,
    c: 3
};

function getValues1 (obj: any, keys: string[]) {
    return keys.map(key => obj[key])
}

getValues1(obj1, ['a', 'b']); // [1, 2]
getValues1(obj1, ['d', 'e']); // [undefined, undefined]

// 当 keys 传入非 obj 中的属性时，会返回 undefined。如何进行约束呢？这里就需要索引类型，索引类型的查询操作符 keyof T 表示类型 T 的所有公共属性的字面量联合类型
interface Obj {
    a: number
    b: string
}
let key: keyof Obj; // let key: "a" | "b"
// 索引访问操作符 T[K] 对象 T 的属性 K 代表的类型
let value: Obj['a']; // let value: number

let obj2 = {
    a: 1,
    b: 2,
    c: 3
}
function getValues2 <T, U extends keyof T>(obj: T, keys: U[]): T[U][] {
    return keys.map(key => obj[key])
}

getValues2(obj2, ['a', 'b']); // [1, 2]
getValues2(obj2, ['d', 'e']); // Type 'string' is not assignable to type '"a" | "b" | "c"'.

```
### 映射类型
```javascript
// 可以讲一个旧的类型生成一个新的类型，比如把一个类型中的所有属性设置成只读。
interface Obj {
    a: string
    b: number
    c: boolean
}

// 接口所有属性设置成只读
type ReadonlyObj = Readonly<Obj>

/**
 * Make all properties in T readonly
 */
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
};

// 接口所有属性设置成可选
type PartialObj = Partial<Obj>

/**
 * Make all properties in T optional
 */
type Partial<T> = {
    [P in keyof T]?: T[P];
};

// 抽取Obj子集
type PickObj = Pick<Obj, 'a' | 'b'>

/**
 * From T, pick a set of properties whose keys are in the union K
 */
type Pick<T, K extends keyof T> = {
    [P in K]: T[P];
};

type RecordObj = Record<'x' | 'y' , Obj>

// 更多映射类型参考 typescript/lib/lib.es5.d.ts 内源码

```
### 条件类型
```javascript
// 形式为 T extends U ? X : Y，如果类型 T 可以赋值为 U 结果就为 X 反之为 Y
type TypeName<T> =
    T extends string ? 'string' :
    T extends number ? 'number' :
    T extends boolean ? 'boolean' :
    T extends undefined ? 'undefined' :
    T extends Function ? 'function' :
    'object'

type T1 = TypeName<string> // type T1 = "string"
type T2 = TypeName<string[]> // type T2 = "object"

// 若 (A | B) extends U ? X : Y 形式，其约等于 (A extends U ? X : Y) | (B extends U ? X : Y)
type T3 = TypeName<string | number> // type T3 = "string" | "number"
// 利用该特性可实现类型过滤。
type Diff<T, U> = T extends U ? never : T

type T4 = Diff<'a' | 'b', 'a'> // type T4 = "b"

// 拆解
// Diff<'a', 'a'> | Diff<'b', 'a'>
// never | 'b'
// 'b'

```
## 类型注解与类型推断
```javascript
// 定义变量(count)时显示声明它的类型,这种方式就叫类型注解。
// 类型注解：是(我们告诉TS变量是什么类型)
let count1: number; // 显示声明count的类型是number
count1 = 123;

// 类型推断：就是TS根据变量的值自动的去分析变量的类型
let count2 = 123; // 根据变量的值，分析出count2的类型是number
// 总结：如果TS能自动分析出变量类型，就使用类型推断，如果不能就使用类型注解

```

## 类型断言
```javascript
<类型>值
// 或
值 as 类型
```
例如：当 TypeScript 不确定一个联合类型的变量到底是哪个类型的时候，我们只能访问此联合类型的所有类型里共有的属性或方法，但是，有些场景我们确实要在不确定类型的情况下去访问一个类型的属性和方法，就可以使用`断言`来判断类型。
```javascript
// 类型断言
function getLength(something: string | number): number {
    // 用法：在需要断言的变量前加上 <Type> 即可
    if ((<string>something).length) { // 使用 类型断言 来判断是否是 string 类型
        return (<string>something).length;
    } else {
        return something.toString().length;
    }
}

// 注意：类型断言不是类型转换，断言成一个联合类型中不存在的类型是不允许的
// function toBoolean(something: string | number): boolean {
//     return <boolean>something; // 联合类型中不存在boolean类型，所以报错
// }

```
## 类型保护
```javascript
// 类型保护的方法有以下几种方式
// 方法1：使用 类型断言 as 来做类型保护
interface Bird {
  fly: boolean;
  sing: () => {};
}

interface Dog {
  fly: boolean;
  bark: () => {};
}

// 类型断言
function Animal(animal: Bird | Dog) {
  if (animal.fly) {
    // 类型断言 as
    (animal as Bird).sing();
  } else {
    (animal as Dog).bark();
  }
}

// 方法2：使用 in 语法来做类型保护
function testAnimal(animal: Bird | Dog) {
  if ("sing" in animal) {
    animal.sing();
  } else {
    animal.bark();
  }
}
// 方法3：使用 typeof 语法来做类型保护
function add1(first: string | number, second: string | number) {
  if (typeof first === "string" || typeof second === "string") {
    return `${first}${second}`;
  }
  return first + second;
}
// 方法4：使用 instanceof 语法来做类型保护
class NumberObj {
  count: number;
  constructor(number: number) {
    this.count = number;
  }
}

function add2(first: object | NumberObj, second: object | NumberObj) {
  // 判断first和second是否是NumberObj的实例
  if (first instanceof NumberObj && second instanceof NumberObj) {
    return first.count + second.count;
  }
  return 0;
}

// 备注:还可以使用其他方法来做类型保护

```
## 字符串字面量类型
```javascript
// 使用type 定了一个字符串字面量类型 EventNames，它只能取三种字符串中的一种
type EventNames = 'click' | 'scroll' | 'mousemove';
function handleEvent(ele: Element, event: EventNames) {
    // do something
}

handleEvent(document.getElementById('hello'), 'scroll');  // 没问题
// handleEvent(document.getElementById('world'), 'dbclick'); // 报错，event 不能为 'dbclick'

```
























