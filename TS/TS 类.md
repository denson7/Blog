[TOC]
# 类
## 类的成员变量和修饰符
```javascript
// 类
class Dog {
    // 构造函数的参数添加类型注解(name: string)
    // 构造函数的返回值默认为当前类自身(Dog)
    constructor(name: string) {
        this.name = name;
    }
    // 类的成员修饰符
    // 默认为public(可省略)
    public name = 'dog'; // 初始值
    run(): void {
        //
    }
    // 私有成员
    private pri(): void {
        //
    }
    // 受保护成员
    protected pro(): void {
        //
    }
    // 声明只读属性,一定要初始化
    readonly legs: number = 4;
    // 类的静态成员,只能通过类名调用,静态属性可以被子类继承
    static food = 'bones';
}

console.log(Dog.prototype); // 不包含name属性
const dog = new Dog('wangwang');
console.log(dog); // name属性只在实例上,不在原型上
// dog.pri(); 报错,不允许实例调用类的私有成员
// dog.pro(); 报错,不允许实例调用类的受保护成员
console.log(Dog.food);

// 类的继承
class WangCai extends Dog {
    constructor(name: string, public color: string) {
        super(name);// super()调用父类中的构造函数
        this.color = color;
        // this.pri(); 子类不允许调用类的私有成员
        this.pro(); // 子类可以调用类的受保护成员
    }
    // 子类自己的属性
    // color: string;
}
console.log(WangCai.food);
```
## 抽象类和多态
```javascript
// 抽象类(只能被继承而不能被实例化的类)
abstract class Animal {
  // 定义方法
  eat() {
    console.log('eat');
  }

  // 抽象方法
  abstract sleep(): void;
}

// let animal = new Animal(); // 报错,抽象类不能被实例化

// 抽象类只能被继承
class Dog1 extends Animal {
  constructor(name: string) {
    super();
    this.name = name;
  }

  name: string;

  run() {}

  // 子类需要实现抽象类中的抽象方法
  sleep() {
    console.log('dog sleep');
  }
}

let d1 = new Dog1('wangwang');
d1.eat();

// 多态
class Cat extends Animal {
  sleep() {
    console.log('cat sleep');
  }
}

let cat = new Cat();

let animals: Animal[] = [d1, cat];

animals.forEach(o => {
  // 程序运行时判断是具体的实例哪一种实例，然后执行不同的方法，这样就实现了多态
  o.sleep();
});

class WorkFlow {
  step1() {
    return this;
  }

  step2() {
    return this;
  }
}

// 方法的链式调用
new WorkFlow().step1().step2();

class Myflow extends WorkFlow {
  next() {
    return this;
  }
}

// this的多态
new Myflow().next().step1().next().step2();
```
### getter/setter
```javascript
// 存取器
const password = '123';
class Employee {
    private _fullName: string;

    get fullName():string {
        return this._fullName;
    }
    set fullName(newName: string) {
        if (password && password == '123') {
            this._fullName = newName;
        } else {
            console.log('Error');
        }
    }
}

const employee = new Employee();
employee.fullName = 'Bob';
console.log(employee.fullName);

// 执行 tsc cls.ts --target es5
```
## 类与接口
```javascript
interface Human {
  name: string;

  eat(): void;
}

class Asian implements Human {
  constructor(name: string) {
    this.name = name;
  }

  name: string;

  // 类实现接口中的方法
  eat(): void {}

  sleep() {}
}


interface Man extends Human{
  run(): void;
}

interface Child {
  cry(): void;
}

interface Boy extends Man, Child{
  //
}

let boy: Boy = {
  name: '',
  run(): void {
    console.log('boy run');
  },
  cry(): void {
    console.log('boy cry');
  },
  eat(): void {
    console.log('boy eat');
  },
};


// 接口继承类
class Auto {
  state = 1;
}

interface AutoInterface extends Auto {
  //
}

class C implements AutoInterface{
  state = 10;
}

class Bus extends Auto implements AutoInterface {
  //
}
```
## 泛型类与泛型约束
```javascript
// 泛型定义函数
function log<T>(value: T): T {
  console.log(value);
  return value;
}

log<string[]>(['a', 'b']);
log(['a', 'b']);

// 泛型定义函数类型别名
// type Log = <T>(value: T) => T;
// let myLog: Log = log;

// myLog('a');
// myLog(123);
// myLog(['aa', 'bb']);


// 泛型定义接口
interface Log<T = string> {
  (value: T): T
}

let myLog: Log<Number> = log;
myLog(1231);


// 泛型类
// 泛型变量放在类的名称后面，就可以约束该类的所有类成员，注意不能约束该类的静态成员
class Log1<T> {
  run(value: T) {
    console.log(value);
    return value;
  }
}

// 实例化该类时，传入泛型T的类型，例如number类型，那么该类的实例方法run方法的参数也就只能是number类型
let log1 = new Log1<number>();
log1.run(123);

// 当不传入泛型T的类型，就表示任意值
let log2 = new Log1();
log2.run('123');
log2.run({});


interface Len {
  length: number
}

// 泛型约束
// 泛型函数test被定义了泛型约束,必须含有length属性,因此它不再是适用于任意类型
function test<T extends Len>(value: T): T {
  console.log(value.length);
  return value;
}
// 传入的参数必须具有length才符合要求
test([123]);
test('12313');
```
## 类型检查机制
```javascript
// 类型推断：不需要指定变量的类型（函数的返回值类型）,TS会根据某些规则自动进行推断出一个类型。（基础类型推断，最佳通用类型推断，上下文类型推断）。
// 类型兼容性：当一个类型Y可以被赋值给另一个类型X时，我们就可以说类型X兼容类型Y。
结构之间兼容：成员少的兼容成员多的
函数之间兼容：参数多的兼容参数少的
// 类型保护：TS能在特定的区块中保证变量属于某种确定的类型。
```






