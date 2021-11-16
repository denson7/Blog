## 同步任务队列
```javascript
const log1 = () => {
  console.log(1)
}
const log2 = () => {
  console.log(2)
}

// 同步队列，最简单的方式就是依次执行，但如果函数不固定改动就大
const pipe1 = (fn1, fn2) => {
  return () => {
    fn1()
    fn2()
  }
}

const log11 = pipe1(log1, log2)
log11()
// 处理不定长参数的问题， 前提是函数之间相互独立，且无参数
const pipe2 = (...fns) => {
  return () => {
    fns.forEach((fn) => {
      fn()
    })
  }
}

const log12 = pipe2(log1, log2)
log12()

// 如何携带参数，并将参数传递给下一个函数，缺陷是只能第一个参数可以传入多个参数，后面的函数不能接收多个参数
// 简单版
const pipe3 = (fn1, fn2, fn3) => {
  return (...args) => {
    let data
    if (fn1) {
      data = fn1(...args)
    }
    if (fn2) {
      data = fn2(data)
    }
    if (fn3) {
      data = fn3(data)
    }
    return data
  }
}
const add = (a, b) => {
  return a + b
}

const sq = n => n * n
const addAndSq = pipe3(add, sq)
addAndSq(1, 1)
// 增强版
const pipe4 = (...fns) => {
  return (...args) => {
    let data = args
    for (let i = 0; i < fns.length; i++) {
      data = [fns[i](...data)]
    }
    return data[0]
  }
}
const addAndSq2 = pipe4(add, sq)
addAndSq2(1, 1)

```



