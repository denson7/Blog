## keep-alive
keep-alive 是 Vue 内置的一个组件，可以实现组件缓存，当组件切换时不会对当前组件进行卸载。
```
属性：
include - 字符串或正则表达式。只有名称匹配的组件会被缓存。
exclude - 字符串或正则表达式。任何名称匹配的组件都不会被缓存。
max - 数字。最多可以缓存多少组件实例。
```
### include和exclude
include指定是否缓存某些组件
```vue
// 指定test组件和about组件被缓存, 属性值是组件的名称即组件的name属性值
<router-view v-slot="{ Component }">
  <transition>
    <keep-alive include="Test, About">
      <component :is="Component"/>
    </keep-alive>
  </transition>
</router-view>
<!-- 或者 -->
<router-view v-slot="{ Component }">
  <keep-alive v-if="$route.meta.keepAlive">
    <component :is="Component" >
      <p>缓存页面</p>
    </component>
  </keep-alive>
  <component :is="Component" v-if="!$route.meta.keepAlive">
    <p>没有缓存的页面</p>
  </component>
</router-view>
```
exclude指定哪些组件不被缓存
```vue
<router-view v-slot="{ Component }">
  <transition>
    <keep-alive exclude="Test">
      <component :is="Component"/>
    </keep-alive>
  </transition>
</router-view>
```

### activated和deactivated
```
两个生命周期 activated/deactivated，用来得知当前组件是否处于活跃状态。
初始进入和离开 created ---> mounted ---> activated --> deactivated
后续进入和离开 activated --> deactivated
```
### 示例
Step1：配置App.vue，这里要注意vue2.x与vue3.0的App.vue配置有差异。
vue2.x中如下
```vue
<template>
  <keep-alive>
    <router-view />
  </keep-alive>
</template>
```
vue3.x中如下
```vue
<template>
  <router-view v-slot="{ Component }">
    <keep-alive v-if="$route.meta.keepAlive">
      <component :is="Component" >
      </component>
    </keep-alive>
    <component :is="Component" v-if="!$route.meta.keepAlive">
    </component>
  </router-view>
</template>
```
Step2：添加meta属性
```javascript
{
  path: '/test',
  name: 'Test',
  meta: {
    keepAlive: true // 设置页面是否需要使用缓存
  },
  component: () => import(/* webpackChunkName: "test" */ '../views/Test.vue')
},
```
