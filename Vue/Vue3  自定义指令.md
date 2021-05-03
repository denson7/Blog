[TOC]
## 自定义指令
```
// 文档
https://v3.cn.vuejs.org/guide/custom-directive.html
```
```vue
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Vue3.0</title>
    <script src="https://unpkg.com/vue@next"></script>
  </head>
  <body>
    <div id="root"></div>
  </body>
  <script>
    // 自定义指令 v-focus 自动聚焦
    const app = Vue.createApp({
      data() {
        return {
          show: true,
          number: 1,
          topNumber: 50,
        };
      },
      // 定义局部指令
      // directives: [],
      mounted() {
        // 原生写法
        // this.$refs.input.focus();
      },
      methods: {},
      template: `
        <div v-pos:test="topNumber">
          <div v-show="show">
            <input ref="input" />
            <br/>
            <input v-focus />
          </div>
        </div>
      `,
    });
    // 全局定义focus标签
    app.directive('pos', {
      // 接收参数
      mounted(ele, binding) {
        console.log(binding.arg, binding.value);
        ele.style.top = binding.value + 'px';
      },
      updated(ele, binding) {
        ele.style.top = binding.value + 'px';
      },
    });
    app.directive('focus', {
      beforeMount() {
        console.log('beforeMount');
      },
      mounted(el) {
        console.log('mounted');
        el.focus();
      },
      beforeUpdate() {
        console.log('beforeUpdate');
      },
      updated() {
        console.log('updated');
      },
      beforeUnmount() {
        console.log('beforeUnmount');
      },
      unmounted() {
        console.log('unmounted');
      },
    });
    const vm = app.mount('#root');
  </script>
</html>

​```
```

