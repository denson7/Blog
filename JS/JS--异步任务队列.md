```javascript
class AsyncQueue {
  constructor() {
    // 任务队列
    this.queue = []
    // 当前是否有任务在执行
    this.running = false
  }
  push(fn) {
    return new Promise((resolve, reject) => {
      // 将传入的fn包装一层，并添加到任务队列中
      this.queue.push(async () => {
        this.running = true
        try {
          const res = await fn()
          resolve(res)
        } catch (error) {
          reject(error)
        }
        this.running = false
        // 上一个任务执行完成后，取出队列中第一个任务进行执行
        this.queue.shift()?.()
      })
      // 当前是否有任务在执行，没有则取出队列中第一个任务执行
      if (!this.running) {
        this.queue.shift()?.()
      }
    })
  }
}
// 睡眠函数
const sleep = t => new Promise(r => setTimeout(r, t))

const q = new AsyncQueue()
q.push(async () =>{
  await sleep(3000)
  console.log(1111)
})
q.push(async () => {
  await sleep(1000)
  console.log(2222)
})


// 模拟接口接口，该接口的访问间隔必须时2秒以上，否则会报错
const callApi = (() => {
  let lastTime = 0
  return async () => {
    if (Date.now() - lastTime < 2 * 1000) {
      throw new Error('访问频繁')
    }
    lastTime = Date.now()
    await sleep(2000)
    return new Date()
  }
})()
// 直接调用10次接口，只有第一次成功，后面9次都是访问失败
new Array(10).fill(1).forEach(async () => {
  const res = await callApi()
  console.log(res)
})
// 使用异步队列去请求接口，保证每次请求之间的间隔大于2秒
const q2 = new AsyncQueue()
new Array(10).fill(1).forEach(async()=>{
  const res = await q2.push(async () => {
    await sleep(2000)
    return callApi()
  })
  console.log(res)
})

```

```javascript
function f() {
  // 距离顶部的高度
  // 滚动条上端距离顶部的距离
  let scrollTop = document.documentElement.scrollTop || document.body.scrollTop
  // 可视区域的高度
  const windowHeight = document.documentElement.clientHeight || document.body.clientHeight
  // 滚动条的总高度（当前可滚动的页面的总高度）
  const scrollHeight = document.documentElement.scrollHeight || document.body.scrollHeight

  // 滚动条到底部
  if (scrollTop + windowHeight >= scrollHeight) {
    // 滑倒底部
    scrollToBottom()
  }
}

// 将滚动条滚动到底部
function scrollToBottom() {
  window.scrollTo({
    top: document.documentElement.scrollHeight || document.body.scrollHeight,
    behavior: "smooth"
  })
}

/**
 * 滚动到指定位置
 *
 * @param number
 * @param time
 * @returns {number}
 */
const scrollToTop = (number = 0, time) => {
  if (!time) {
    document.body.scrollTop = document.documentElement.scrollTop = number
    return number;
  }
  const spacingTime = 20; // 设置循环的间隔时间  值越小消耗性能越高
  let spacingIndex = time / spacingTime // 计算循环的次数
  let nowTop = document.body.scrollTop + document.documentElement.scrollTop // 获取当前滚动条位置
  let everTop = (number - nowTop) / spacingIndex // 计算每次滑动的距离
  let scrollTimer = setInterval(() => {
    if (spacingIndex > 0) {
      spacingIndex--
      scrollToTop(nowTop += everTop)
    } else {
      clearInterval(scrollTimer) // 清除计时器
    }
  }, spacingTime)
}

// 滚动到距离页面顶部xx的位置 动画时间为5秒
scrollToTop(document.body.scrollHeight, 5000);

// 溢出多余的html标签内容
function removeUselessDom() {
  const removeClasses = ['a', 'b']
  removeClasses.forEach(className => removeDomByClassName(className))
}

/**
 * 根据className类名移除dom
 *
 * @param className 类名
 */
function removeDomByClassName(className) {
  const elements = document.getElementsByClassName(className)
  // 倒着删除
  for (let i = elements.length - 1; i >= 0; i--) {
    elements[i].parentNode.removeChild(elements[i])
  }
}

/**
 * 根据标签和类名来删除DOM元塞
 *
 * @param tagName 标签名 li
 * @param className 类名 item-box
 */
function removeDomByTagName(tagName, className) {
  const elements = document.getElementsByTagName(tagName)
  for (let i = elements.length - 1; i > 0; i--) {
    if (elements[i].className === className) {
      elements[i].parentNode.removeChild(elements[i])
    }
  }
}


// 等待多张图片加载完再执行代码 js

function getAllImageSrc(){
  const result = []
  const body = document.body.innerHTML
  const reg = new RegExp(/http:\/\/hiphotos.baidu.com.*?\.png/g)
  return body.match(reg)
}

// 等待多张图片加载完再执行代码 js
// 页面图片加载完毕以后执行JS代码 imagesloaded

function doImageLoadFinish(arr = [], cb) {
  let i = 0
  let timer = null
  const len = arr.length
  const load = src => {
    if (i < len) {
      const img = new Image()
      img.src = src
      timer = setInterval(function (){
        if (img.complete) {
          clearInterval(timer)
          load(arr[i])
        }
      }, 10)
    } else {
      // 加载完成执行代码
      cb()
    }
  }
  load(arr[0])
}

doImageLoadFinish(['x', 'x'], () => {
  console.log('所有图片加载完毕')
})

```

