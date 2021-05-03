[TOC]
## computed
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
    // data & methods & computed & watch
    // computed 和 method 都能实现的功能，建议使用computed， 因为有缓存
    // computed 和 watcher 都能实现的功能，建议使用computed，因为更加简洁
    const app = Vue.createApp({
      data() {
        return {
          message: '手机',
          number: 5,
          price: 200,
          newTotal: 1000,
        };
      },
      // 计算属性
      // 缺点：不能进行异步操作
      computed: {
        // 当计算属性依赖的内容发生变更时，才会重新执行计算
        total() {
          console.log(Date.now());
          return this.number * this.price;
        },
      },
      // 普通方法
      methods: {
        // 注意: 在methods中定义方法不建议用箭头方法，因为箭头函数的this指向最外层window, 而非Vue这个实例
        handleClick() {
          console.log('handleClick', this);
        },
        // 实现效果与computed的total属性一样
        // 区别是 getTotal() 只要页面重新渲染就会重新计算，例如更改message的值，也会重新计算一遍
        getTotal() {
          console.log(Date.now());
          return this.number * this.price;
        },
      },
      // 监听器
      watch: {
        // price发生变化时，函数会执行
        price(current, prev) {
          console.log(current, prev);
          this.newTotal = current * this.number;
          // setTimeout(() => {
          //   console.log('price change');
          // }, 1000);
        },
      },
      // 属性的绑定使用 v-bind，可以简写为 :
      template: `
        <div
          @click="handleClick"
        >{{message}}: {{total}}, {{getTotal()}}, {{newTotal}}</div>
      `,
    });
    const vm = app.mount('#root');
  </script>
</html>

```
