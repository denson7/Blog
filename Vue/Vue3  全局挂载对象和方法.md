## Vue3 挂在全局对象或方法
Vue3.x已经不支持直接Vue.prototype.$xxx =xxx这种方式来挂载全局对象。
### 示例
```javascript
import { createApp } from 'vue';
import App from './App.vue';
import router from './router';
import store from './store';

const app = createApp(App);
// Vue3 全局挂载对象或方法
app.config.globalProperties.$AppName = 'Learn-Vue3';
app.config.globalProperties.$Test = () => {
  console.log('this is test function');
  return 'test';
};
app
  .use(store)
  .use(router)
  .mount('#app');

```
```vue
<script lang="ts">
import { defineComponent, getCurrentInstance } from 'vue';

export default defineComponent({
  name: 'About',
  setup () {
    // 获取方法通过 getCurrentInstance 获
    const { proxy, ctx }: any = getCurrentInstance();
    // 方法1 通过proxy获取
    console.log(proxy.$AppName);
    const test = proxy.$Test();
    console.log(test);
    // 方法2 通过ctx获取
    console.log(ctx.$AppName);
    return {};
  }
});
</script>
```
