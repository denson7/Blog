## 自定义封装组件
ColumnList.vue
```javascript
<template>
  <div class="row">
    <div v-for="column in columnList" :key="column.id" class="col-4 mb-4">
      <div class="card h-100 shadow-sm">
        <div class="card-body text-center">
          <img  :src="column.avatar" :alt="column.title" class="rounded-circle border border-light w-25 my-3" >
          <h5 class="card-title">{{column.title}}</h5>
          <p class="card-text text-left">{{column.description}}</p>
          <router-link :to="{name: 'column', params: {id: column.id}}" class="btn btn-outline-primary">查看详情</router-link>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, PropType, computed } from 'vue';

export interface ColumnProps {
  id: number;
  title: string;
  avatar?: string;
  description: string;
}

export default defineComponent({
  name: 'ColumnList',
  props: {
    list: {
      type: Array as PropType<ColumnProps[]>,
      required: true
    }
  },
  setup (props, context) {
    console.log('params:', props, context);
    const columnList = computed(() => {
      return props.list.map(column => {
        // 如果头像没有传递就使用默认值
        if (!column.avatar) {
          column.avatar = require('@/assets/logo.png');
        }
        console.log(column);
        return column;
      });
    });
    return { columnList };
  }
});
</script>
<style scoped>
  .img-item {
    width: 100px;
    height: 100px;
  }
</style>

```
home.vue
```javascript
<template>
  <div class="container">
    <Header :user="user"></Header>
    <column-list :list="list"></column-list>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import ColumnList, { ColumnProps } from '@/components/ColumnList.vue';
import Header, { UserProps } from '@/components/Header.vue';

const testData: ColumnProps[] = [
  { id: 1, title: 'test1', avatar: '', description: 'this is description1' },
  { id: 2, title: 'test2', avatar: '', description: 'this is description2' },
  { id: 3, title: 'test3', avatar: '', description: 'this is description3' }
];

const userData: UserProps = {
  id: 100,
  name: 'zzz',
  isLogin: true
};

export default defineComponent({
  name: 'Home',
  components: {
    ColumnList,
    Header
  },
  setup () {
    return {
      list: testData,
      user: userData
    };
  }
});
</script>
```
header.vue
```javascript
<template>
  <nav class="navbar navbar-dark bg-primary justify-content-between mb-4 px-4">
    <a class="navbar-brand" href="#">顶部</a>
    <ul v-if="!user.isLogin" class="list-inline mb-0">
      <li class="list-inline-item"><a href="#" class="btn btn-outline-light my-2">登陆</a></li>
      <li class="list-inline-item"><a href="#" class="btn btn-outline-light my-2">注册</a></li>
    </ul>
    <ul v-else class="list-inline mb-0">
      <li class="list-inline-item">
        <dropdown :title="`你好，${user.name}`"></dropdown>
      </li>
    </ul>
  </nav>
</template>
<script lang="ts">
import { defineComponent, PropType } from 'vue';
import Dropdown from './Dropdown.vue';

export interface UserProps {
  isLogin: boolean;
  name?: string;
  id?: number;
}

export default defineComponent({
  name: 'Header',
  props: {
    user: {
      type: Object as PropType<UserProps>,
      required: true
    }
  },
  components: {
    Dropdown
  }
});
</script>

<style scoped>
</style>
```
Dropdown.vue
```javascript
<template>
  <div class="dropdown" ref="dropdownRef" :class="`${isOpen ? 'a' : 'b'} cc`" >
    <a href="#" class="btn btn-outline-light my-2 dropdown-toggle" @click.prevent="toggleOpen">
      {{title}}
    </a>
    <ul class="dropdown-menu" :style="{display: 'block'}" v-if="isOpen">
      <li class="dropdown-item">
        <a href="#">新建文章</a>
      </li>
      <li class="dropdown-item">
        <a href="#">编辑资料</a>
      </li>
    </ul>
</div>
</template>

<script lang="ts">
// eslint-disable-next-line @typescript-eslint/no-unused-vars
import { defineComponent, ref, onMounted, onUnmounted, watch } from 'vue';
import useClickOutside from '../hooks/useClickOutside';

export default defineComponent({
  name: 'Dropdown',
  props: {
    title: {
      type: String,
      required: true
    }
  },
  setup () {
    const isOpen = ref(false);
    const toggleOpen = () => {
      isOpen.value = !isOpen.value;
    };
    // 获取dom节点,需要保持命名名称的一致, 类型时联合类型，初始为null,正常为HTMLElement
    const dropdownRef = ref<null | HTMLElement>(null);
    // 方法1
    // const handleClick = (e: MouseEvent) => {
    //   console.log(e.target);
    //   // 拿到dropdown的DOM元素后判断点击的内容是否被这个元素包含
    //   // 在dropdown内部什么都不做，在外部则需要关闭下拉框
    //   // 判断ref是否存在
    //   if (dropdownRef.value) {
    //     // 判断点击的元素是否包含点击的元素 node.contains(otherNode)
    //     if (!dropdownRef.value.contains(e.target as HTMLElement)) {
    //       isOpen.value = false;
    //     }
    //   }
    // };
    // // 在mounted时添加click事件
    // onMounted(() => {
    //   document.addEventListener('click', handleClick);
    // });
    // // 在unmounted时将事件删除
    // onUnmounted(() => {
    //   document.removeEventListener('click', handleClick);
    // });

    // 方法2 使用自定义的hook
    const isClickOutside = useClickOutside(dropdownRef);
    watch(isClickOutside, () => {
      if (isOpen.value && isClickOutside.value) {
        isOpen.value = false;
      }
    });
    return {
      isOpen,
      toggleOpen,
      dropdownRef
    };
  }
});
</script>
<style scoped>
</style>

```
useClickOutside.ts
```javascript
import { ref, onMounted, onUnmounted, Ref } from 'vue';

const useClickOutside = (elementRef: Ref<null | HTMLElement>) => {
  const isClickOutside = ref(false);
  const handleClick = (e: MouseEvent) => {
    if (elementRef.value) {
      if (elementRef.value.contains(e.target as HTMLElement)) {
        isClickOutside.value = false;
      } else {
        isClickOutside.value = true;
      }
    }
  };
  // 在mounted时添加click事件
  onMounted(() => {
    document.addEventListener('click', handleClick);
  });
  // 在unmounted时将事件删除
  onUnmounted(() => {
    document.removeEventListener('click', handleClick);
  });
  return isClickOutside;
};

export default useClickOutside;

```
ValidateForm.vue
```javascript
<template>
  <form class="validate-form-container">
    <slot name="default"></slot>
    <div class="submit-area" @click.prevent="submitForm">
      <slot name="submit">
        <button type="submit" class="btn btn-primary">提交</button>
      </slot>
    </div>
  </form>
</template>
<script lang="ts">
import { defineComponent, onUnmounted } from 'vue';
import mitt from 'mitt';
type ValidateFunc = () => boolean

// 实例化 mitt
export const emitter = mitt();
export default defineComponent({
  emits: ['form-submit'],
  setup (props, context) {
    let funcArr: ValidateFunc[] = [];
    const submitForm = () => {
      // 循环执行数组 得到最后的验证结果
      const result = funcArr.map(func => func()).every(result => result);
      context.emit('form-submit', result);
    };
    // 将监听得到的验证函数都存到一个数组中
    // const callback = (func: ValidateFunc): void => {
    //   funcArr.push(func);
    // };
    const callback = (func:any): void => {
      funcArr.push(func);
    };
    // 添加监听
    emitter.on('form-item-created', callback);
    onUnmounted(() => {
      // 删除监听
      emitter.off('form-item-created', callback);
      funcArr = [];
    });
    return {
      submitForm
    };
  }
});
</script>

<style scoped>
</style>
```
ValidateInput.vue
```javascript
<template>
  <div class="validate-input-container pb-3">
    <!--$attrs 绑定父组件传递的多余属性  -->
    <input
      class="form-control"
      :class="{'is-invalid': inputRef.error}"
      :value="inputRef.val"
      @blur="validateInput"
      @input="updateValue"
      v-bind="$attrs"
    />
    <span v-if="inputRef.error" class="invalid-feedback">{{inputRef.message}}</span>
  </div>
</template>

<script lang="ts">
import { defineComponent, PropType, reactive, onMounted } from 'vue';
import { emitter } from './ValidateForm.vue';

export interface RuleProp {
    type: 'required' | 'email' | 'password' | 'custom';
    message: string;
    validate?: () => boolean;
  }
export type TagType = 'input' | 'textarea';
export type RulesProp = RuleProp[];

const emailReg = /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,4}$/i;

export default defineComponent({
  name: 'ValidateInput',
  // 禁用属性继承
  inheritAttrs: false,
  props: {
    rules: Array as PropType<RulesProp>,
    modelValue: String,
    tag: {
      type: String as PropType<TagType>,
      default: 'input'
    }
  },
  setup (props, context) {
    const inputRef = reactive({
      val: props.modelValue || '',
      error: false,
      message: ''
    });
    const validateInput = () => {
      // console.log('check', props.rules, inputRef.val);
      if (props.rules) {
        const allPass = props.rules.every((rule) => {
          let passed = true;
          inputRef.message = rule.message;
          switch (rule.type) {
            case 'required':
              passed = (inputRef.val.trim() !== '');
              break;
            case 'email':
              passed = emailReg.test(inputRef.val);
              break;
            case 'password':
              passed = (inputRef.val.length >= 6);
              break;
            case 'custom':
              passed = rule.validate ? rule.validate() : true;
              break;
            default:
              break;
          }
          return passed;
        });
        inputRef.error = !allPass;
        return allPass;
      }
      return true;
    };
    const updateValue = (e: KeyboardEvent) => {
      const targetValue = (e.target as HTMLInputElement).value;
      inputRef.val = targetValue;
      context.emit('update:modelValue', targetValue);
    };
    // 将事件发射出去，其实就是把验证函数发射出去
    onMounted(() => {
      emitter.emit('form-item-created', validateInput);
    });
    return { inputRef, validateInput, updateValue };
  }
});
</script>

<style scoped>
</style>

```
Signup.vue
```javascript
<template>
      <div class="signup-page mx-auto p-3 w-330">
      <validate-form @form-submit="onFormSubmit">
        <div class="mb-3">
          <label class="form-label">邮箱地址</label>
          <validate-input
            :rules="emailRules"
            v-model="formData.email"
            placeholder="请输入邮箱地址"
            type="text"
          />
        </div>
        <div class="mb-3">
          <label class="form-label">昵称</label>
          <validate-input
            :rules="nameRules"
            v-model="formData.nickName"
            placeholder="请输入昵称"
            type="text"
          />
        </div>
        <div class="mb-3">
          <label class="form-label">密码</label>
          <validate-input
            type="password"
            placeholder="请输入密码"
            :rules="passwordRules"
            v-model="formData.password"
          />
        </div>
        <div class="mb-3">
          <label class="form-label">重复密码</label>
          <validate-input
            type="password"
            placeholder="请再次密码"
            :rules="repeatPasswordRules"
            v-model="formData.repeatPassword"
          />
        </div>
        <template #submit>
          <button type="submit" class="btn btn-primary btn-block btn-large">
            注册新用户
          </button>
        </template>
      </validate-form>
    </div>
</template>

<script lang="ts">
import { defineComponent, reactive } from 'vue';
import ValidateInput, { RulesProp } from '@/components/ValidateInput.vue';
import ValidateForm from '@/components/ValidateForm.vue';

export default defineComponent({
  name: 'Signup',
  components: {
    ValidateForm,
    ValidateInput
  },
  setup () {
    const formData = reactive({
      email: '',
      nickName: '',
      password: '',
      repeatPassword: ''
    });
    const emailRules: RulesProp = [
      // 多种规则
      { type: 'required', message: '电子邮箱地址不能为空' },
      { type: 'email', message: '请输入正确的电子邮箱格式' }
    ];
    const nameRules: RulesProp = [{ type: 'required', message: '昵称不能为空' }];
    const passwordRules: RulesProp = [
      { type: 'required', message: '密码不能为空' }
    ];
    const repeatPasswordRules: RulesProp = [
      { type: 'required', message: '重复密码不能为空' },
      // 自定义规则
      {
        type: 'custom',
        validate: () => {
          return formData.password === formData.repeatPassword;
        },
        message: '密码不相同'
      }
    ];
    const onFormSubmit = (result: any) => {
      if (result) {
        alert('🎉 通过啦');
      } else {
        alert('😢 没通过');
      }
    };
    return {
      emailRules,
      nameRules,
      passwordRules,
      repeatPasswordRules,
      onFormSubmit,
      formData
    };
  }
});
</script>
<style scoped>
  .w-330 {
    max-width: 600px;
  }
</style>

```