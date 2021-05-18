# 修饰符
## v-model修饰符
### .lazy
可以将@input事件变成@blur事件
```javascript
<input v-model.lazy="msg" />
```
### .number
只能输入数字值
```javascript
<input v-model.number="msg" />
```
### .trim
自动过滤输入的首尾空格
```javascript
 <input v-model.trim="msg" />
```

## 事件修饰符
### .stop
阻止事件继续传播
### .prevent
阻止标签默认行为
### .capture
使用事件捕获模式,即元素自身触发的事件先在此处处理，然后才交由内部元素进行处理
### .self
只当在 event.target 是当前元素自身时触发处理函数
### .once
事件将只会触发一次
### .passive
告诉浏览器你不想阻止事件的默认行为
### .native
有时候我们想在子组件上面监听一些事件，比如click，但是不论你怎么点，它都没反应，为什么呢？
```vue
<template>
  <div>
    <Child @click="test"></Child>
  </div>
</template>
<script>
export default {
  methods: {
  test(){}
}
}
</script>
// 因为这样写vue会认为，你自定义了一个click事件，要在子组件通过$emit('click')触发才行。如果我就是要在父组件触发呢？那就要用到native修饰符了。
<template>
  <div>
    <Child @click.native="test"></Child>
  </div>
</template>
<script>
export default {
  methods: {
    test(){}
  }
}
</script>
```
## 键盘事件的修饰符
```
.enter
.tab
.delete (捕获“删除”和“退格”键)
.esc
.space
.up
.down
.left
.right
```
### 系统修饰键
```
.ctrl
.alt
.shift
.meta
```
### 鼠标按钮修饰符
```
.left
.right
.middle
```
## .sync
如果你想要在父组件控制一个子组件的显示隐藏，是不是还在传一个prop和一个自定义方法，这样会很麻烦，不妨试一试sync修饰符.
```vue
 // 父组件
<template>
<div>
<Toggle :show.sync = 'show'></Toggle>
</div>
</template>
//Toggle 组件
<template>
  <div>
    <div v-if="show">
      展示和隐藏组件
    </div>
    <button @click="test">隐藏组件</button>
  </div>
</template>
<script>
export default {
  props:['show'],
  methods: {
    test(){
      this.$emit('update:show',false)
    }
  }
}
</script>
```

