- [Jest 单元测试](#Jest 单元测试)
  * [安装依赖](#安装依赖)
  * [常用说明](#常用说明)
  * [普通单元测试](#普通单元测试)
  * [Koa单元测试](#koa)
  

## Jest 单元测试
```
// 官网地址
https://jestjs.io/docs/en/getting-started
```
### 安装依赖
```powershell
$ npm install -D jest ts-jest babel-jest babel-preset-env @types/jest @babel/core @babel/preset-env
```
### 常用说明
```js
# 4 个主要的生命周期函数：
afterAll(fn, timeout): 当前文件中的所有测试执行完成后执行 fn, 如果 fn 是 promise，jest 会等待 timeout 毫秒，默认 5000.
beforeAll(fn, timeout): 同 afterAll，在所有测试开始前执行
beforeEach(fn, timeout): 同 afterEach，在每个测试开始前执行
afterEach(fn, timeout): 每个 test 执行完后执行 fn，timeout 含义同上

分组（Test Group）：descripe(描述语, function)
测试用例（Test Case）：test(描述语, function)
断言（Assert）：expect(运行需测试的方法并返回实际结果).toBe(预期结果)

# 断言示例: 更多断言参考地址：https://jestjs.io/docs/en/expect.html
// toBe(value)： 比较数字、字符串
// toEqual(value)： 比较对象、数组
// toBeNull()
// toBeUndefined
// toHaveProperty(keyPath, value)： 是否有对应的属性
// toContain(item)： 是否包含对应的值，括号里写上数组、字符串
// toMatch(regexpOrString)： 括号里写上正则
// toBeFalsy()：六个falsy值：false，0，''，null， undefined，和NaN。其他一切都是Truthy
// toBeTruthy()
// toBeGreaterThan(number)： 大于
// toBeLessThan(number)： 小于

```
### 普通单元测试
```js
import {respMsg,  getId, isEqualZero} from '../helper';

describe('test utils function', () => {
  test('test function respMsg', () => {
    const obj = {};
    const result = respMsg(obj);
    expect(result).toEqual({code: 200, msg: 'Success'});
  });


  test('test function getId', () => {
    const obj = {
      dateTime: 1575968239,
      IMEI: 123456789234567
    };
    const result = getId(obj);
    expect(result).toBe(123458365202806);
  });


  test('test function isEqualZero', () => {
    const obj = {
      longitude: 0,
      latitude: 0
    };
    const result = isEqualZero(obj);
    expect(result).toBeTruthy();
  });
});
```
### Koa单元测试
```js
// package.json
"scripts": {
  "dev": "cross-env NODE_ENV=development nodemon --watch 'src/**/*' -e ts,tsx --exec 'ts-node' ./src/index.ts",
  "tslint": "tslint --fix ./src/**/*.ts",
  "test": "jest --coverage"
},
"jest": {
  "moduleFileExtensions": [
    "ts",
    "tsx",
    "js"
  ],
  "transform": {
    "\\.(ts|tsx)$": "ts-jest"
  },
  "testRegex": "/__test__/.*\\.(ts|tsx|js)$"
},
// app.ts
const Koa = require('koa');
const Router = require('koa-router');
const router = new Router();

const app = new Koa();

router.get('/', async ctx => {
  ctx.body = 'API Online';
});

router.post('/post', async ctx => {
  ctx.body = {
    code: 200,
    msg: 'Success'
  };
});

app.use(router.routes());

const demo = app.listen(8081).on('error', err => {
  console.error(err);
});

module.exports = demo;
// __test__/app.spec.js
const server = require('../src/index');
const request = require('supertest');

afterEach(() => {
  server.close();
});

describe('test Controller function', () => {
  test('test function post', async () => {
    const response = await request(server)
      .post('/post')
      .send(
        {
          key: 'name',
          value: 'test'
        }
      );
    expect(response.body).toEqual({code: 200, msg: 'Success'});
  });
  test('test function get', async () => {
    const response = await request(server)
      .get('/')
    expect(response.body).toBe('API Online');
  });
});
```
```bash
$ npm run test--> 实际执行的命令为：jest --coverage
```

**说明**

```
%stmts是语句覆盖率（statement coverage）：是不是每个语句都执行了
%Branch分支覆盖率（branch coverage）：是不是每个if代码块都执行了
%Funcs函数覆盖率（function coverage）：是不是每个函数都调用了
%Lines行覆盖率（line coverage）：是不是每一行都执行了
```