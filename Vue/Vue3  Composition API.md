
## 响应式API
实现todolist
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
    // 关于 list 操作进行封装
    const listRelativeEffect = () => {
      const { reactive } = Vue;
      // 将非基础类型的数据(array, object)转化为响应式引用
      const list = reactive([]);
      const addOne = (item) => {
        list.push(item);
      };
      return { list, addOne };
    };
    // 关于 inputValue 操作进行封装
    const inputRefEffect = () => {
      const { ref } = Vue;
      // 将基础类型的数据 (string, number, boolean) 转化为响应式引用
      const inputValue = ref('');
      const handleInputChange = (e) => {
        inputValue.value = e.target.value;
      };
      return { inputValue, handleInputChange };
    };

    const app = Vue.createApp({
      setup() {
        const { list, addOne } = listRelativeEffect();
        const { inputValue, handleInputChange } = inputRefEffect();
        return {
          list,
          addOne,
          inputValue,
          handleInputChange,
        };
      },
      template: `
        <div>
          <div>
            <input :value="inputValue" @input="handleInputChange"/>
            <button @click="()=> addOne(inputValue)">点击</button>
            <ul>
              <li v-for="(item, index) in list" key="index">{{item}}</li>
            </ul>
          </div>
        </div>`,
    });
    const vm = app.mount('#root');
  </script>
</html>

```

