[TOC]
## setup 用法
### 基本用法
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
    // setup 函数 基本用法
    // ref reactive 响应式的引用
    // 原理：通过 proxy 对数据进行封装，当数据发生变化时，触发模板等内容的更新
    // ref 处理基础类型的数据 (string, number, boolean) 转化为响应式引用
    // ref 处理非基础类型的数据 (array, object) 转化为响应式引用
    const app = Vue.createApp({
      methods: {
        test() {
          // 即实例上的方法可以调用setup，但是setup不能调用实例上的方法
          console.log(this.$options.setup());
        },
      },
      mounted() {
        this.test();
      },
      // setup()在创建实例完全初始化之前，所以在setup函数内不能使用this
      // 但是在 methods 中，可以获取到setup的返回值
      setup(props, context) {
        // this.test(); 错误写法, 因为在实例化之前test()还未放到this上

        // context 用法
        const { attrs, slots, emit } = context;
        console.log(attrs); // None-Props 属性
        console.log(slots.default()); // 虚拟DOM, 可以return () => h("div", {}, slots.default())
        // console.log(emit("change")); // 向父组件触发事件
        const { ref, reactive, toRefs } = Vue;
        // proxy, "Lily" 变成 proxy({value: "Lily"}) 这样的一个响应式引用
        let name = ref('Lily');
        setTimeout(() => {
          name.value = 'John';
        }, 3000);
        // proxy, { userName: 'Michelle' } 变成 proxy({ userName: 'Michelle' }) 这样的一个响应式引用
        const nameObj = reactive({ userName: 'Michelle' });
        setTimeout(() => {
          nameObj.userName = 'Lucy';
        }, 3000);
        // 解构使用 toRefs
        // proxy({userName: 'Michelle'}) --> proxy({userName: proxy({value: 'Michelle'})})
        const { userName } = toRefs(nameObj);
        // toRef 可以设置默认值
        // toRefs 不能设置默认值
        // 返回值可以直接在模板中使用
        return {
          name,
          nameObj,
          userName,
          handleClick: () => {
            console.log('setup click');
          },
        };
      },
      template: `
        <div @click="handleClick">
            Hello {{name}}, {{nameObj.userName}}--{{userName}}
        </div>`,
    });

    const vm = app.mount('#root');
  </script>
</html>
```
### 实现computed 计算属性功能
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
    // 使用setup 来实现computed 计算属性功能
    const app = Vue.createApp({
      setup() {
        const { ref, computed, reactive } = Vue;
        const count = ref(0);
        const numObj = reactive({number: 10});
        const handleClick = () => {
          count.value += 1;
          numObj.number += 10;
        };
        // computed 中设置 get 和 set
        let countAddFive = computed({
          get: () => {
            //  ref 类型，需要.value获取值
            return count.value + 5;
          },
          set: (data) => {
            count.value = data;
          },
        });
        // computed 简单写法
        const numObjAddFive = computed(()=> {
            // reactive 类型,不需要.value获取值,直接获取即可
            return numObj.number + 10;
        });
        setTimeout(() => {
          countAddFive.value = 100;
        }, 3000);
        return { count, numObj, countAddFive, numObjAddFive, handleClick };
      },
      template: `
        <div>
          <div>
            <p>{{count}} -- {{countAddFive}}</p>
            <p>{{numObj.number}}--{{numObjAddFive}}</p>
            <button @click="handleClick">点击</button>
          </div>
        </div>`,
    });
    const vm = app.mount('#root');
  </script>
</html>
```
### 实现watch 监听器功能
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
    // 使用setup 来实现 watch 监听器功能
    // watchEffect 监听器 偏向于 effect
    const app = Vue.createApp({
      setup() {
        const { ref, reactive, toRefs, watch, watchEffect } = Vue;
        const name = ref('John');
        // watch
        // 首次加载不会立即执行, 只有依赖值发生变化时才会执行
        // 参数可以拿到原始值和当前值
        // 需要传递参数
        watch(
          name,
          (currentValue, preValue) => {
            console.log('ref类型的watch', currentValue, preValue);
          },
          // immediate 参数或将watch 参数变为立即执行, 默认为false
          { immediate: true }
        );
        const obj = reactive({ userName: 'Lily', nickName: 'pepper' });
        const { userName, nickName } = toRefs(obj);
        // 当监听多个参数时,参数为数组形式
        const stop1 = watch(
          [() => obj.userName, () => obj.nickName],
          ([curName, curNickName], [preName, prevNickName]) => {
            console.log(
              'reactive类型的watch',
              curName,
              preName,
              curNickName,
              prevNickName
            );
            setTimeout(() => stop1(), 4000);
          }
        );

        // watchEffect 与 watch 的区别
        // 立即执行
        // 不需要传递你要监听的内容,它会自动检测内部是否存在依赖,如果有会自动执行
        // watchEffect 不能获取之前数据的值
        watchEffect(() => {
          console.log(obj.userName, obj.nickName);
        });
        // 如果要取消监听
        // const stop = watchEffect(() => {
        //   console.log(obj.userName, obj.nickName);
        //   setTimeout(() => {
        //     stop(); // 3秒后watchEffect()方法将失效, watch 类似
        //   }, 3000);
        // });
        return { name, userName, nickName };
      },
      template: `
        <div>
           <div>
            <input v-model="name"/> {{name}}
           </div>
           <div>
             <input v-model="userName"/> {{userName}} <br/>
             <input v-model="nickName"/> {{nickName}}
           </div>
        </div>`,
    });
    const vm = app.mount('#root');
  </script>
</html>
```
### 实现生命周期功能
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
    // 使用setup 来实现 生命周期函数功能
    const app = Vue.createApp({
      // 无法实现 beforeCreate 和 created 因为setup函数执行的时间点在它们俩之间
      // beforeMount === onBeforeMount
      // mounted === onMounted
      // beforeUpdate === onBeforeUpdate
      // updated === onUpdated
      // beforeUnmount === OnBeforeUnmount
      // unmounted === OnUnmounted
      setup() {
        const {
          ref,
          onBeforeMount,
          onMounted,
          onBeforeUpdate,
          onUpdated,
          OnBeforeUnmount,
          OnUnmounted,
          onRenderTracked,
          onRenderTriggered,
        } = Vue;
        const name = ref('John');
        onBeforeMount(() => {
          console.log('onBeforeMount');
        });
        onMounted(() => {
          console.log('onMounted');
        });
        onBeforeUpdate(() => {
          console.log('onBeforeUpdate');
        });
        onUpdated(() => {
          console.log('onUpdated');
        });
        OnBeforeUnmount(() => {
          console.log('OnBeforeUnmount');
        });
        OnUnmounted(() => {
          console.log('OnUnmounted');
        });
        // 每次渲染后重新收集响应式依赖
        onRenderTracked(() => {
          console.log('onRenderTracked');
        });
        // 每次触发页面重新渲染时执行
        onRenderTriggered(() => {
          console.log('onRenderTriggered');
        });
        const handleClick = () => {
          name.value = 'Lily';
        };
        return { name, handleClick };
      },
      template: `
        <div @click="handleClick">
            Hello {{name}}
        </div>`,
    });
    const vm = app.mount('#root');
  </script>
</html>
```
### 使用provide和inject，获取DOM节点
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
    // 在setup函数中 使用 Provide Inject
    const app = Vue.createApp({
      setup() {
        const { ref, provide, readonly, onMounted } = Vue;
        const name = ref('John');
        // 传递变量 name, 子组件仅能只读数据
        provide('name', readonly(name));
        provide('changeName', (value) => {
          name.value = value;
        });
        // 获取真实的 DOM 元素节点
        // 定义与模板中ref属性同名的变量名
        const domDiv = ref(null);
        onMounted(() => {
          console.log(domDiv.value); // <div>获取DOM</div>
          console.log(domDiv.value.innerHTML); // 获取DOM
        });
        return { domDiv };
      },
      template: `
        <div>
            <child />
            <div ref="domDiv">获取DOM</div>
        </div>`,
    });
    // 子组件
    app.component('child', {
      setup() {
        const { inject } = Vue;
        // 使用inject接收provide传递的值
        const name = inject('name');
        // 接收函数, 父组件的值使用父组件的方法修改
        const changeName = inject('changeName');
        const handleClick = () => {
          changeName('Lily');
        };
        return { name, handleClick };
      },
      template: `
        <div @click="handleClick"><p>Child: {{name}}</p></div>`,
    });
    const vm = app.mount('#root');
  </script>
</html>
```
