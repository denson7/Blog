[TOC]
## Suspence 异步组件
### 示例
```js
# 定义一个异步组件 setup() 返回promise
<template>
  <h1>{{ result }}</h1>
</template>

<script lang="ts">
import { defineComponent } from "vue";
export default defineComponent({
  name: "AsyncShow",
  setup() {
    /** 使用Suspense, setup()需要返回一个Promise,  */
    return new Promise((resolve) => {
      setTimeout(() => {
        return resolve({
          result: 100,
        });
      }, 3000);
    });
  },
});
</script>
# 定义一个异步组件 setup() 使用async awati
<template>
  <img :src="result && result.url" alt="" />
</template>

<script lang="ts">
import { defineComponent } from "vue";
import axios from "axios";

export default defineComponent({
  name: "AsyncShowAwait",
  async setup() {
    const resp = await axios.get(
      "https://images.dog.ceo/breeds/terrier-irish/n02093991_4404.jpg"
    );
    console.log(resp);
    return {
      result: resp.data,
    };
  },
});
</script>
# 使用 suspence 组件渲染
<template>
  <Suspense>
    <template #default>
      <div>
        <async-show></async-show>
        <async-show-await></async-show-await>
      </div>
    </template>
    <template #fallback>
      <div>Loading... ---- 异步加载中</div>
    </template>
  </Suspense>
</template>

<script>
import AsyncShow from "./AsyncShow.vue";
import AsyncShowAwait from "./AsyncShowAwait.vue";
export default {
  components: { AsyncShow, AsyncShowAwait },
  name: "Home",
};
</script>
```
