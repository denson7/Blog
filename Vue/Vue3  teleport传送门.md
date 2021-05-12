[TOC]
## teleport 
```
// 文档
https://v3.cn.vuejs.org/guide/teleport.html
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
    <style>
      .area {
        position: absolute;
        width: 200px;
        height: 300px;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        background: #ababab;
      }
      .mask {
        position: absolute;
        left: 0;
        right: 0;
        top: 0;
        bottom: 0;
        opacity: 0.5;
        background: grey;
      }
    </style>
    <script src="https://unpkg.com/vue@next"></script>
  </head>
  <body>
    <div id="root"></div>
  </body>
  <script>
    // teleport 传送门 可以将组件内某些元素挂载到指定目标上
    const app = Vue.createApp({
      data() {
        return {
          number: 50,
          show: false,
        };
      },
      mounted() {},
      methods: {
        hanleClickBtn() {
          // div.mask 这个div遮罩层目前在div.area内部，现在想把这个遮罩层布满整个屏幕，就可以使用 teleport(传统门) 这个功能，将div.mask这个div挂载body根节点下面
          this.show = !this.show;
        },
      },
      template: `
        <div class="area">
          <button @click="hanleClickBtn">点击</button>
          <teleport to="body">
            <div class="mask" v-show="show">{{number}}</div>
          </teleport>
        </div>
      `,
    });
    const vm = app.mount('#root');
  </script>
</html>

```
