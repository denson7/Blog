[TOC]
## CompositionAPI 使用 Vuex
```vue
#main.js
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'
import store from './store'

createApp(App).use(store).use(router).mount('#app')
# App.vue
<template>
  <div>
    <router-link to="/">Home</router-link> |
    <router-link to="/about">About</router-link>
  </div>
  <router-view />
</template>
# route.js
import { createRouter, createWebHashHistory } from 'vue-router';
import Home from '../views/Home.vue';

const routes = [
  {
    path: '/',
    name: 'Home',
    component: Home,
  },
  {
    path: '/about',
    name: 'About',
    component: () =>
      import(/* webpackChunkName: "about" */ '../views/About.vue'),
  },
];

const router = createRouter({
  history: createWebHashHistory(),
  routes,
});

export default router;
# store.js
import { createStore } from 'vuex';

export default createStore({
  state: {
    name: 'Lily',
  },
  mutations: {
    // 同步修改
    changeName(state, params) {
      state.name = params;
    },
  },
  actions: {
    // 异步修改
    asyncChangeName(store, params) {
      setTimeout(() => {
        store.commit('changeName', params);
      }, 2000);
    },
  },
  modules: {},
});

#Home.vue
<template>
  <div @click="handleClick">
    <h1>{{ name }}</h1>
  </div>
</template>

<script>
import { toRefs } from "vue";
import { useStore } from "vuex";
export default {
  name: "Home",
  setup() {
    const store = useStore();
    const { name } = toRefs(store.state);
    const handleClick = () => {
      // store.commit("changeName", "Lucy");
      store.dispatch("asyncChangeName", "Hello");
    };
    return { name, handleClick };
  },
};
</script>
# About.vue
<template>
  <div class="about">
    <h1>This is an about page</h1>
    <h2>{{ name }}</h2>
  </div>
</template>
<script>
import { toRefs } from "vue";
import { useStore } from "vuex";
export default {
  name: "About",
  setup() {
    const store = useStore();
    const { name } = toRefs(store.state);
    return { name };
  },
};
</script>
```
