## 自定义localStorage函数
```javascript
// localStorage
export const storage = {
  // setItem: (key, value) => localStorage.setItem(key, JSON.stringify(value)),
  setItem: (key = "", value = "", expires = 0) => {
    let obj = {
      name: key,
      value: value,
      expires: expires * 1000, // 单位为秒
      startTime: new Date().getTime()
    };
    if (obj.expires) {
      localStorage.setItem(obj.name, JSON.stringify(obj));
    } else {
      localStorage.setItem(obj.name, JSON.stringify(obj.value));
    }
  },
  getItem: (key) => {
    let obj = localStorage.getItem(key);
    const item = JSON.parse(obj);
    let date = new Date().getTime();
    if (item && item.startTime) {
      if (date - item.startTime > item.expires) {
        localStorage.removeItem(key);
        return false;
      } else {
        return item.value;
      }
    } else {
      return item;
    }
  },
  // getItem1: (key) => JSON.parse(localStorage.getItem(key)),
  // 移除单个
  removeItem: (key) => localStorage.removeItem(key),
  // 移除全部
  clear: () => localStorage.clear()
};
```

## localStorage 监听
```javascript
// localStorage.setItem监听：自定义事件 setItemEvent
// localStorage.getItem监听：自定义事件 getItemEvent
// localStorage.removeItem监听：自定义事件 removeItemEvent

// 自定义监听函数 setItemEvent
// 对localStorage.setItem监听
window.addEventListener("setItemEvent", function(e) {
  console.log(e.detail);
});

// localStorage.getItem监听
window.addEventListener("getItemEvent", function(e) {
  console.log(e.detail);
});

// localStorage.removeItem监听
window.addEventListener("removeItemEvent", function(e) {
  console.log(e.detail);
});


// 监听自定义事件 setItemEvent
localStorage.setItem = (Orgin => {
  return function(key, value) {
    let setItemEvent = new CustomEvent("setItemEvent", { detail: { setKey: key, value } });
    window.dispatchEvent(setItemEvent);
    Orgin.call(this, key, typeof value === "string" ? value : JSON.stringify(value));
  };
})(localStorage.setItem);

// 监听自定义事件 getItemEvent
localStorage.getItem = (Orgin => {
  return function(key) {
    let result = JSON.parse(Orgin.call(this, key));
    let getItemEvent = new CustomEvent("getItemEvent", { detail: { getKey: key, value: result } });
    window.dispatchEvent(getItemEvent);
    return result;
  };
})(localStorage.getItem);


// 监听自定义事件 removeItemEvent
localStorage.removeItem = (Orgin => {
  return function(key) {
    let removeItemEvent = new CustomEvent("removeItemEvent", { detail: { removeKey: key } });
    window.dispatchEvent(removeItemEvent);
    Orgin.call(this, key);
  };
})(localStorage.removeItem);
```
