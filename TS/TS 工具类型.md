[TOC]
[https://juejin.im/post/6866403741608722445?utm_source=gold_browser_extension](https://juejin.im/post/6866403741608722445?utm_source=gold_browser_extension)
## Partial
利用 TypeScript 内置的工具类型 `Partial<T>` 来快速把某个接口类型中定义的属性变成可选的。
```javascript
interface Info {
    name: string;
    age: number;
}

/**
 * type InfoOption = {
 *   name?: string | undefined;
 *   age?: number | undefined;
 * }
 */
type InfoOption = Partial<Info>

/**
 * Partial源码实现
 * Make all properties in T optional
 */
type Partial<T> = {
    [P in keyof T]?: T[P];
};

```
## Required
使用`Required<T> `快速的把所有的可选的属性变成必选的。
```javascript
interface Info {
    name: string;
    age: number;
}

/**
 * type InfoOption = {
 *   name?: string | undefined;
 *   age?: number | undefined;
 * }
 */
type InfoOption = Partial<Info>

/**
 * type PullDownRefresh = {
 *   threshold: number;
 *   stop: number;
 * }
 */
type InfoRequire = Required<Partial<Info>>


/**
 * Required源码实现
 * 通过 -? 移除了可选属性中的 ?，使得属性从可选变为必选的
 * Make all properties in T required
 */
type Required<T> = {
    [P in keyof T]-?: T[P];
};

```
## Record
构造一个键为 K, 值为 T 的键值对类型。
```javascript
// keyof any 包含: string | number | symbol
type Record<K extends keyof any, T> = {
    // 表示键类型是约束于 K (string | number | symbol) 中的一种或多种，值类型为 T
    // in 表示遍历
    [P in K]: T
}

const foo: Record<string, boolean> = {
    a: true
}
const bar: Record<'x' | 'y', number> = {
    x: 1,
    y: 2
}

// Record将petsGroup中的每个值(dog | cat | fish)都转为 IPetInfo 类型。
type petsGroup = 'dog' | 'cat' | 'fish';
interface IPetInfo {
    name:string,
    age:number,
}

type IPets = Record<petsGroup | 'otherAnimal', IPetInfo>;

const animalsInfo:IPets = {
    dog:{
        name:'dogName',
        age:2
    },
    cat:{
        name:'catName',
        age:3
    },
    fish:{
        name:'fishName',
        age:5
    },
    otherAnimal:{
        name:'otherAnimalName',
        age:10
    }
}

```
## Pick
主要用于提取接口的某几个属性。
```javascript
interface Todo {
    title: string
    completed: boolean
    description: string
}

type TodoPreview = Pick<Todo, "title" | "completed">

const todo: TodoPreview = {
    title: 'Clean room',
    completed: false
}

```
## Omit
Omit 的作用刚好和 Pick 相反。
```javascript
interface Todo {
    title: string
    completed: boolean
    description: string
}

// 排除 title
type TodoPreview = Omit<Todo, "title">

const todo: TodoPreview = {
    description: 'Clean room',
    completed: false
}

```
## Exclude
排除掉 T 中能赋值给 U 的类型。
```javascript
// 如果 T 是 U 的子类型，则返回 never, 否则返回 T
type Exclude<T, U> = T extends U ? never : T

// 'a' | 'b' | 'c' 中排除掉 'b', 只能为 'a' 或 'c'
let foo: Exclude<'a' | 'b' | 'c', 'b'> = 'a'
foo = 'c'

```
## Extract
与 Exclude 相反，提取 T 中能赋值给 U 的类型。
```javascript
// 如果 T 是 U 的子类型，则返回 T, 否则返回 never
type Extract<T, U> = T extends U ? T : never

// 'a' | 'b' | 'c' 中提取 'b', 只能为 'b'
let foo: Extract<'a' | 'b' | 'c', 'b'> = 'b'

```
## NonNullable
排除掉 null 、undefined 类型。
```javascript
// 排除掉 null | undefined
type NonNullable<T> = T extends null | undefined ? never : T

type Foo = string | null
const a: NonNullable<Foo> = 'a' // 不能赋值给 null 或 undefined

```
## 示例
```javascript
interface Person {
    name: string;
    sex: string;
    age?: number;
}

// name和age都是可填项
type Person1 = Partial<Person>;
// name和age都是必填项
type Person2 = Required<Person>;
// name和age都是只读项
type Person3 = Readonly<Person>;
// 仅选择age属性
type Person4 = Pick<Person, "age">;
// 排除name属性
type Person5 = Omit<Person, "name">;
// 将name和age转为string,组成新的对象
type Person6 = Record<'name' | 'age', string>

```



