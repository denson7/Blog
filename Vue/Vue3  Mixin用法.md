[TOC]
## Mixin 混入
```
// 文档
https://v3.cn.vuejs.org/guide/mixins.html
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
    // mixin 混入
    // 组件自身的 data，methods 优先级高于 mixin 的data，methods 优先级
    // 生命周期函数，先执行 mixin 里面的，再执行组件里面的生命周期函数
    // 自定义属性，默认情况组件的属性优先级 高于 mixin属性的优先级
    const myMixmin = {
      total: 20,
      data() {
        return {
          number: 2,
        };
      },
      methods: {
        handleClick() {
          console.log('mixins handleClick');
        },
      },
      created() {
        console.log('mixins created');
      },
    };
    const app = Vue.createApp({
      total: 10, // 自定义属性, 访问使用 this.$options.total
      data() {
        return {
          number: 1,
        };
      },
      // 当组件没有data时，会使用mixin中的data
      mixins: [myMixmin], // 局部 mixins
      created() {
        console.log('created');
      },
      methods: {
        handleClick() {
          console.log('handleClick');
        },
      },
      template: `
        <div>
          <div>{{number}}</div>
          <button @click="handleClick">点击</button>
          <p>{{this.$options.total}}</p>
        </div>
      `,
    });

    // 修改自定义属性的优先级 合并策略
    app.config.optionMergeStrategies.total = (mixinVal, appValue) => {
      return mixinVal || appValue;
    };

    // 定义一个全局的mixin
    app.mixin({
      data() {
        return {
          number: 2,
        };
      },
      methods: {
        handleClick() {
          console.log('mixins handleClick');
        },
      },
      created() {
        console.log('mixins created');
      },
    });
    const vm = app.mount('#root');
  </script>
</html>

```
