```
classAsyncQueue{
 constructor() {
   // 任务队列
   this.queue= []
   // 当前是否有任务在执行
   this.running=false
  }
 push(fn) {
   returnnewPromise((resolve,reject)=>{
     // 将传入的fn包装一层，并添加到任务队列中
     this.queue.push(async()=>{
       this.running=true
       try{
         constres=awaitfn()
         resolve(res)
        }catch(error) {
         reject(error)
        }
       this.running=false
       // 上一个任务执行完成后，取出队列中第一个任务进行执行
       this.queue.shift()?.()
      })
     // 当前是否有任务在执行，没有则取出队列中第一个任务执行
     if(!this.running) {
       this.queue.shift()?.()
      }
    })
  }
}
// 睡眠函数
constsleep=t=>newPromise(r=>setTimeout(r,t))

constq=newAsyncQueue()
q.push(async()=>{
 awaitsleep(3000)
 console.log(1111)
})
q.push(async()=>{
 awaitsleep(1000)
 console.log(2222)
})


// 模拟接口接口，该接口的访问间隔必须时2秒以上，否则会报错
constcallApi= (()=>{
 letlastTime=0
 returnasync()=>{
   if(Date.now() -lastTime<2*1000) {
     thrownewError('访问频繁')
    }
   lastTime=Date.now()
   awaitsleep(2000)
   returnnewDate()
  }
})()
// 直接调用10次接口，只有第一次成功，后面9次都是访问失败
newArray(10).fill(1).forEach(async()=>{
 constres=awaitcallApi()
 console.log(res)
})
// 使用异步队列去请求接口，保证每次请求之间的间隔大于2秒
constq2=newAsyncQueue()
newArray(10).fill(1).forEach(async()=>{
 constres=awaitq2.push(async()=>{
   awaitsleep(2000)
   returncallApi()
  })
 console.log(res)
})


```

