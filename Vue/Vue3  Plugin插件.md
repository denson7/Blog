[TOC]
## 自定义插件
```
// 文档
https://v3.cn.vuejs.org/guide/plugins.html
```
示例
```html
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
    // plugin 插件 也是把同用性的功能封装起来
    const myPlugin = {
      // 定义plugin必须要写一个install方法, app.use(myPlugin)时会执行install()方法
      // 参数1为 Vue实例, 参数2为传递的参数
      install(app, options) {
        console.log(app, options);
        // 例如 为app扩展一些属性
        app.provide('name', 'You');
        // 扩展自定义标签
        app.directive('focus', {
          mounted(el) {
            console.log(el);
            el.focus();
          },
        });
        // 扩展mixin
        app.mixin({
          mounted() {
            console.log('myPlugin, mixin');
          },
        });
        // 扩展全局属性
        app.config.globalProperties.$sayHello = 'nihao';
      },
    };
    const app = Vue.createApp({
      template: `<my-title>hello world</my-title>`,
    });

    app.component('my-title', {
      inject: ['name'],
      mounted() {
        console.log(this.$sayHello);
      },
      template: `<div>
        {{name}}
        <slot/>
        <input v-focus />
        </div>`,
    });
    // 使用插件
    app.use(myPlugin, { name: 'test' });
    const vm = app.mount('#root');
  </script>
</html>

```