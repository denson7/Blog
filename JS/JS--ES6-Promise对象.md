[TOC]
## Promise

所谓`Promise`，简单说就是一个`容器`，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。从语法上说，`Promise `是一个`对象`，从它可以获取异步操作的消息。

---
**两个特点：**
1.对象的状态不受外界影响。Promise对象代表一个异步操作，有三种状态：`pending（进行中）`、`fulfilled（已成功）`和`rejected（已失败）`。只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。这也是Promise这个名字的由来，它的英语意思就是`“承诺”`，表示其他手段无法改变。
2.一旦状态改变，就不会再变，任何时候都可以得到这个结果。`Promise`对象的状态改变，只有两种可能：从`pending`变为`fulfilled`和从`pending`变为`rejected`。

---

### 基本用法
```javascript
const promise = new Promise(function(resolve, reject) {
  // ... some code
  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }
});

```
`Promise`构造函数接受一个函数作为参数，该函数的两个参数分别叫做`resolve`和`reject`。
`resolve`函数的作用是：将`Promise`对象的状态从`“未完成”`变为`“成功”`（即从 pending 变为 resolved），在异步操作成功时调用，并将异步操作的结果，作为参数传递出去。
`reject`函数的作用是：将`Promise`对象的状态从`“未完成”`变为`“失败”`（即从 pending 变为 rejected），在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去。

`Promise`实例生成以后，可以用`then`方法分别指定`resolved`状态和`rejected`状态的回调函数。
```javascript
promise.then(function(value) {
  // success
}, function(error) {
  // failure
});
// then方法可以接受两个回调函数作为参数。
// 第一个回调函数是Promise对象的状态变为resolved时调用，
// 第二个回调函数是Promise对象的状态变为rejected时调用。
// 其中，第二个函数是可选的，不一定要提供。
```
```javascript
let promise = new Promise(function(resolve, reject) {
  console.log('Promise');
  resolve();
});
promise.then(function() {
  console.log('resolved.');
});
console.log('Hi!');
//查看这段代码，打印顺序是怎样的呢？依次输出：Promise,Hi!,resolved
//Promise
//Hi!
//resolved.
//解释：因为Promise 新建后立即执行，所以首先输出的是Promise。然后，then方法指定的回调函数，将在当前脚本所有同步任务执行完才会执行，所以resolved最后输出
```
### 示例
```javascript
// Promise基本结构
new Promise((resolve, reject) => {
  // 异步函数
  $.ajax({
    url:'xxxx',
    type:'post',
    success(res){
      resolve(res);
    },
    error(err){
      reject(err);
    }
  });
}).then((res)=>{
  console.log('success', res);
},(err)=>{
  console.log('error:', err);
});

// 链式操作
var promiseFn1 = new Promise((resolve, reject) => {
  // 异步函数
  $.ajax({
    url:'xxxx',
    type:'post',
    success(res){
      resolve(res);
    },
    error(err){
      reject(err);
    }
  });
});

var promiseFn2 = new Promise((resolve, reject) => {
  // 异步函数
  $.ajax({
    url:'xxxx',
    type:'post',
    success(res){
      resolve(res);
    },
    error(err){
      reject(err);
    }
  });
});

promiseFn1.then((res) => {
  console.log('promiseFn1 success')
  return promiseFn2
}).then(() =>{
  console.log('promiseFn2 success')
})
```

### Promise.prototype.then()
```javascript
getJSON("/posts.json").then(function (json) {
  return json.post;
}).then(function (post) {
  // ...
});
```

### Promise.prototype.catch()
`Promise.prototype.catch`方法是`.then(null, rejection)`的别名，用于指定发生错误时的回调函数。
```javascript
getJSON('/posts.json').then(function(posts) {
  // ...
}).catch(function(error) {
  // 处理 getJSON 和 前一个回调函数运行时发生的错误
  console.log('发生错误！', error);
});
```
### Promise.prototype.all()
将多个Promise对象实例包装，生成并返回一个新的Promise实例。
promise数组中所有的promise实例都变为resolve的时候，该方法才会返回。
并将所有结果传递results数组中。
promise数组中任何一个promise为reject的话，则整个Promise.all调用会立即终止，并返回一个reject的新的promise对象。

```javascript
Promise.all([
  $.ajax({url:'/data/arr.txt', dataType:'json'}),
  $.ajax({url:'/data/json.txt', dataType:'json'}),
  $.ajax({url:'/data/num.txt', dataType:'json'}),
]).then((results) => {
  const [arr, json, num] = results
  console.log(arr, json, num);
}, (err) => {
  console.log(err)
})
```
### Promise.prototype.race()
Promse.race就是赛跑的意思。哪个结果获得的快，就返回那个结果。不管结果本身是成功状态还是失败状态。
对于普通 Promise 来说，如何处理超时问题，可以使用setTimeout来封装一个Promise，然后再使用Promise.race来对两个Promise进行处理，谁先跑完就返回谁，如果setTimeout封装的promise先返回，则判断另一个Promise超时了。
```javascript

function waitWithTimeout(promise, timeout, timeoutMessage = "timeout") {
  let timer;
  const timeoutPromise = new Promise((_, reject) => {
    timer = setTimeout(() => reject(timeoutMessage), timeout);
  });

  return Promise.race([timeoutPromise, promise])
    .finally(() => clearTimeout(timer));    // 别忘了清 timer
}

// 示例
(async () => {
  const business = new Promise(resolve => setTimeout(resolve, 1000 * 10));

  try {
    await waitWithTimeout(business, 1000);
    console.log("[Success]");
  } catch (err) {
    console.log("[Error]", err);    // [Error] timeout
  }
})()
```

### 用例
#### 1.限制异步操作的并发个数并尽可能快的完成全部
需求：有8个图片资源的url，已经存储在数组urls中。urls类似于['https://image1.png', 'https://image2.png', ....]，而且已经有一个函数function loadImg，输入一个url链接，返回一个Promise，该Promise在图片下载完成的时候resolve，下载失败则reject。但有一个要求，任何时刻同时下载的链接数量不可以超过3个。请写一段代码实现这个需求，要求尽可能快速地将所有图片下载完成。
```javascript
var urls = [
  "https://xx/img1.png",
  "https://xx/img2.png",
  "https://xx/img3.png",
  "https://xx/img4.png",
  "https://xx/img5.png",
  "https://xx/img6.png",
  "https://xx/img7.png",
  "https://xx/img8.png",
];
function loadImg(url) {
  return new Promise((resolve, reject) => {
    const img = new Image();
    img.onload = function () {
      console.log("一张图片加载完成");
      resolve(img);
    };
    img.onerror = function () {
      reject(new Error('Could not load image at' + url));
    };
    img.src = url;
  });
}
```
分析：既然题目的要求是保证每次并发请求的数量为3，那么我们可以先请求urls中的前面三个(下标为0,1,2)，并且请求的时候使用Promise.race()来同时请求，三个中有一个先完成了(例如下标为1的图片)，我们就把这个当前数组中已经完成的那一项(第1项)换成还没有请求的那一项(urls中下标为3)。直到urls已经遍历完了，然后将最后三个没有完成的请求(也就是状态没有改变的Promise)用Promise.all()来加载它们。
```javascript
function limitLoad(urls, handler, limit) {
  let sequence = [].concat(urls); // 复制urls
  // 这一步是为了初始化 promises 这个"容器"
  let promises = sequence.splice(0, limit).map((url, index) => {
    return handler(url).then(() => {
      // 返回下标是为了知道数组中是哪一项最先完成
      return index;
    });
  });
  // 注意这里要将整个变量过程返回，这样得到的就是一个Promise，可以在外面链式调用
  return sequence
    .reduce((pCollect, url) => {
      return pCollect
        .then(() => {
          return Promise.race(promises); // 返回已经完成的下标
        })
        .then(fastestIndex => { // 获取到已经完成的下标
          // 将"容器"内已经完成的那一项替换
          promises[fastestIndex] = handler(url).then(
            () => {
              return fastestIndex; // 要继续将这个下标返回，以便下一次变量
            }
          );
        })
        .catch(err => {
          console.error(err);
        });
    }, Promise.resolve()) // 初始化传入
    .then(() => { // 最后三个用.all来调用
      return Promise.all(promises);
    });
}

limitLoad(urls, loadImg, 3)
  .then(res => {
    console.log("图片全部加载完毕");
    console.log(res);
  })
  .catch(err => {
    console.error(err);
  });
```
#### 2.如何让异步操作顺序执行
需求：有一个数组[1, 2, 3, 4]，如何利用promise，每隔一秒，输出一个数字。
备注：

```javascript
const arr = [1, 2, 3, 4];
// 使用promise顺序输出1,2,3,4
// 方法1：
arr.reduce((p, x) => p.then(() => new Promise(r => setTimeout(() => r(console.log(x)), 1000))), Promise.resolve());

// 方法2：
const promises = [];

arr.map(async (value) => {
  promises.push(new Promise((res) => {
    setTimeout(() => {
      console.log(value)
      res()
    }, 1000)
  }))
});

const promise = Promise.resolve();

for (var i = 0; i < promises.length; i += 1) {
  const task = promises[i];
  promise.then(() => {
      return task
    })
}
```
#### 3. 练习
```javascript
// 练习1
const p1 = new Promise((resolve, reject) => {
  console.log('p1'); // 注意,这里没有返回
});
console.log('1', p1); // 1 Promise {<pending>}
// 说明: 从上至下，先遇到new Promise，执行该构造函数中的代码p1,然后同步执行代码console，输出1，此时p1没有被resolve或者reject，因此状态还是pending

// 练习2
const p2 = new Promise((resolve, reject) => {
  console.log(1);
  resolve('success');
  console.log(2);
});
p2.then(() => {
  console.log(4);
});
console.log(3); // 1 2 3 4
// 说明：从上至下，先遇到new Promise，执行代码，输出1，再遇到resolve('success')， 将promise的状态改为了resolved并且将值保存下来，继续执行代码输出2，跳出promise，往下执行，碰到promise.then这个微任务，将其加入微任务队列，继续执行代码输出3，当前执行顺序的宏任务全部执行完毕，检查微任务队列，发现promise.then这个微任务且状态为resolved，执行它,输出4。

// 练习3
const p3 = new Promise((resolve, reject) => {
  console.log(1);
  console.log(2);
});
p3.then(() => {
  console.log(3); // 这里不会执行
});
console.log(4); // 1 2 4
// 说明：promise中并没有resolve或者reject，所以promise.then并不会执行，它只有在被改变了状态之后才会执行。

// 练习4
const p4 = new Promise((resolve, reject) => {
  console.log(1);
  resolve('4');
});
const p41 = p4.then(res => {
  console.log(res);
});
console.log(2, p4); // 2 Promise {<resolved>: "resolve1"}
console.log(3, p41); // 3 Promise {<pending>}
// 1 2 3 4
// 说明：先执行new Promise构造函数中的代码，输出1，碰到resolve函数, 将p4的状态改变为resolved, 并将结果保存下，碰到p4.then这个微任务，将它放入微任务队列，p41是一个新的状态为pending的Promise，接着输出1和p4的状态是resolved，输出2和p41的状态pending。宏任务执行完毕，检查微任务队列，发现p4.then这个微任务且状态为resolved，执行它,输出4

// 练习5
console.log('1');
setTimeout(() => {
  console.log('4');
});
Promise.resolve().then(() => {
  console.log('3');
});
console.log('2');
// 1 2 3 4
// 说明：setTimout是一个宏任务被放入宏任务队列，Promise.then是一个微任务被放入微任务队列，本次宏任务执行完，先检查微任务，发现Promise.then，执行它，再执行下一个宏任务。

// 练习6
const p6 = new Promise((resolve, reject) => {
  console.log(1);
  setTimeout(() => {
    console.log(4);
    resolve(6);
    console.log(5);
  }, 0);
  console.log(2);
});
p6.then((res) => {
  console.log(res);
});
console.log(3);
// 1 2 3 4 5 6

// 练习7
setTimeout(() => {
  console.log(2);
  Promise.resolve().then(() => {
    console.log(3);
  });
}, 0);
setTimeout(() => {
  console.log(4);
}, 0);
console.log(1);
// 1 2 3 4
// Pomise.then()是微任务，在执行完当次的setTimeOut宏任务完成后继续执行

// 练习8
Promise.resolve().then(() => {
  console.log(2);
  const timer2 = setTimeout(() => {
    console.log(5);
  }, 0);
});
const timer1 = setTimeout(() => {
  console.log(3);
  Promise.resolve().then(() => {
    console.log(4);
  });
}, 0);
console.log(1);
// 1 2 3 4 5
// 说明：遇到timer1，先加入到宏任务队列，执行到微任务时又遇到新的宏任务，再添加到宏任务队列(FIFO)

// 练习9
const p9 = new Promise((resolve, reject) => {
  resolve('success1'); // 只有第一次resolve 或 reject有效
  reject('error'); // 无效
  resolve('success2'); // 无效
});
p9.then(res => {
  console.log('then: ', res); // success1
}).catch(err => {
  console.log('catch:', err);
});
// 说明：构造函数中的 resolve 或 reject 只有第一次执行有效，多次调用没有任何作用。 即：Promise的状态一经改变就不能再改变。

// 练习10
Promise.resolve(1)
  .then(2)
  .then(Promise.resolve(3))
  .then(console.log);
// 1
// 说明：.then 或者 .catch 的参数期望是函数，传入非函数则会发生值穿透。第一个then和第二个then中传入的都不是函数，一个是数字，一个是对象，因此发生了穿透，将resolve(1) 的值直接传到最后一个then里。

// 练习11
async function a2() {
  console.log(2);
}
async function a1() {
  console.log(1);
  await a2(); // 这里会等待a2的代码执行完毕以后，才会往下继续执行console.log(4)
  console.log(4);
}
a1();
console.log(3);
// 1 2 3 4
// 说明：调用a1,输出1,碰到await,它会阻塞a1后面代码,会先去执行a2中的代码输出2，然后跳出a1,输出3,在一轮宏任务全部执行完之后,再来执行刚刚await后面的内容输出4
// 注意与new Promise()的区别是new Promise()不会阻塞后面的代码

// 练习12
async function async2() {
  setTimeout(() => {
    console.log(5);
  }, 0);
  console.log(2);
}
async function async1() {
  console.log(1);
  await async2();
  console.log(4);
}

async1();
console.log(3);
// 1 2 3 4 5
// 说明：定时器始终还是最后执行的，它被放到下一条宏任务的延迟队列中

// 练习13
async function fn() {
  // return await 1234
  // 等同于
  return 123;
}
fn().then(res => console.log(res));
// 说明：正常情况下，async中的await命令是一个Promise对象，返回该对象的结果。但如果不是Promise对象的话，就会直接返回对应的值，相当于Promise.resolve()

// 注意：在async1中await后面的Promise是没有返回值的，也就是它的状态始终是pending状态，因此相当于一直在await，await却始终没有响应...
async function async1 () {
  console.log(2);
  await new Promise(resolve => {
    console.log(3);
  });
  console.log(5);
  return 6;
}
console.log(1);
async1().then(res => console.log(res));
console.log(4);
// 仅输出：1 2 3 4

// 练习14
async function testSometing() {
  console.log(2);
  return 5;
}

async function testAsync() {
  console.log(6);
  return Promise.resolve(8);
}

async function test() {
  console.log(1);
  const v1 = await testSometing();
  console.log(v1);
  const v2 = await testAsync();
  console.log(v2);
}

test();

var p14 = new Promise(resolve => {
  console.log(3);
  resolve(7);
});
p14.then(val => console.log(val));
console.log(4);
// 1 2 3 4 5 6 7 8
// 说明：注意微任务队列加入顺序
```
#### 4.循环遍历中使用promise,如何在所有异步操作执行完后，再执行下一步
```javascript
  await Promise.all(
    records.map(async (o) => {
      const info = await getUserInfoByZenithId(o.user_id);
      data.push({
        user_name: o.user_name,
        job_number: o.user_id,
        ic_number: '',
        mobile: info['phone'] || '',
        body_temperature: o.body_temperature,
        sign_time: o.check_time,
        ...o,
      });
    })
  );

```
```javascript
// 使用for of
// 使用 for循环
// 使用map
// 注意：不要和forEach一起使用await，而是使用for循环，也不要在 filter 和 reduce 中使用 await，使用map
```
#### 5.Promise顺序执行
```javascript
// `promises` is an array of `Promise`
const run = (promises) =>
  promises.reduce(
    (p, c) => p.then((rp) => c.then((rc) => [...rp, rc])),
    Promise.resolve([])
  );

// Example
run(promises).then((results) => {
  // `results` is an array of promise results in the same order
});

```
#### 6.限制Promise并发个数 （并行限制promise）
例如：库async-pool、es6-promise-pool、p-limit
```javascript
/**
 * 关键点
 * 1. new promise 一经创建，立即执行
 * 2. 使用 Promise.resolve().then 可以把任务加到微任务队列，防止立即执行迭代方法
 * 3. 微任务处理过程中，产生的新的微任务，会在同一事件循环内，追加到微任务队列里
 * 4. 使用 race 在某个任务完成时，继续添加任务，保持任务按照最大并发数进行执行
 * 5. 任务完成后，需要从 doingTasks 中移出
 */
function limit(count, array,callback) {
  const tasks = []
  const doingTasks = []
  let i = 0
  const enqueue = () => {
    if (i === array.length) {
      return Promise.resolve()
    }
    const task = Promise.resolve().then(() =>callback(array[i++]))
    tasks.push(task)
    const doing = task.then(() => doingTasks.splice(doingTasks.indexOf(doing), 1))
    doingTasks.push(doing)
    const res = doingTasks.length >= count ? Promise.race(doingTasks) : Promise.resolve()
    return res.then(enqueue)
  };
  return enqueue().then(() => Promise.all(tasks))
}

// test
const timeout = i => new Promise(resolve => setTimeout(() => resolve(i), i))
limit(2, [1000, 1000, 1000, 1000], timeout).then((res) => {
  console.log(res)
})
https://juejin.im/post/6854573217013563405
https://juejin.cn/post/6918604733099802638?utm_source=gold_browser_extension
```

#### 7.循环中使用promise
```javascript
export const listFile = async (result = []) => {
  const obj = await Promise.all(
    result.map(async (item) => {
      const fileName = item.name.replace(`${folder}/`, '');
      const tags = await getTag(fileName, 'faceID');
      return {
        name: fileName,
        size: formatBytes(item.size),
        dateFrom: tags.from ? tags.from : '',
        dateTo: tags.to ? tags.to : '',
        modified: item.lastModified,
      };
    })
  );
  return obj;
};

```






















