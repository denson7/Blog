## 浏览器断网情况下，如果处理
### 检测浏览器是否连网
```javascript
// 直接使用
navigator.onLine
// true online
// false offline
// 可以通过network的online选项切换为offline，打印navigator.onLine验证
```
### 检测网络状况
```javascript
navigator.connection 它继承自NetworkInformation API。
// 例如
console.log(navigator.connection) //  {onchange: null, effectiveType: "3g", rtt: 350, downlink: 1.5, saveData: false}
// rtt和downlink是反映网络状况的参数,它们不是定值，而是实时变化的。上一秒可能是rtt 100ms，2.2Mb/s，下一秒就变成400ms，1Mb/s了，onchange 是变更网络事件
// rtt： 连接预估往返时间，单位为ms，值越小网速越快
// downlink：带宽预估值，单位为Mbit/s，值越大网速越快
```
### 如何检测网络变化去做出响应呢
NetworkInformation继承自EventTarget，可以通过监听change事件去做一些响应。
```javascript
var connection = navigator.connection;
var type = connection.effectiveType;

function updateConnectionStatus() {
    console.log("网络状况从 " + type + " 切换至" + connection.effectiveType);
    type = connection.effectiveType;
}

connection.addEventListener('change', updateConnectionStatus);
```

### 注册上线，下线事件的方式
建议window+addEventListener的组合。
```javascript
window.addEventListener('load', function() {
    /*
      <div id="status"></div>
      <div id="log"></div>
    * */
    var status = document.getElementById("status");
    var log = document.getElementById("log");

    function updateOnlineStatus(event) {
        var condition = navigator.onLine ? "online" : "offline";
        status.innerHTML = condition.toUpperCase();

        // insertAdjacentHTML是在标签节点的邻近位置插入
        log.insertAdjacentHTML("beforeend", "Event: " + event.type + "; Status: " + condition);
    }

    window.addEventListener('online',  updateOnlineStatus);
    window.addEventListener('offline', updateOnlineStatus);
});
```

### React组件示例
```javascript
// offlineHandle.js
import React, { useState, useEffect } from "react";
import { notification } from "antd";
import "antd/dist/antd.css";
import "./index.css";

const OfflineHandle = (props) => {
    const {
        offlineTitle = "网络已断开，请检查网络连接。",
        onlineTitle = "网络已连接",
        desc,
        duration = 4.5
    } = props;
    const [mask, setMask] = useState(false);

    const eventHandler = (event) => {
        const type = event.type === "offline" ? "error" : "success";
        console.log(desc, "desc");
        openNotification({
            type,
            title: type === "error" ? offlineTitle : onlineTitle,
            desc: type === "error" ? desc : "",
            duration
        });
        setTimeout(() => {
            setMask(event.type === "offline");
        }, 1500);
    };

    const openNotification = ({ type, title, desc, duration }) => {
        notification[type]({
            message: title,
            description: desc,
            duration
        });
    };

    useEffect(() => {
        window.addEventListener("offline", eventHandler);
        window.addEventListener("online", eventHandler);
        return () => {
            window.removeEventListener("offline", eventHandler);
            window.removeEventListener("online", eventHandler);
        };
    }, []);

    const renderOfflineMask = () => {
        if (!mask) return null;
        return (
            <div className="offline-mask">
                <h2 className="offline-mask-title">{offlineTitle}</h2>

                <p className="offline-mask-desc">{desc}</p >
            </div>
        );
    };

    return <>{renderOfflineMask()}</>;
};

export default OfflineHandle;
```
### Vue 组件示例
```javascript
<!--OfflineHandle.vue-->
<template>
  <div v-if="mask" class="offline-mask">
    <h2 class="offline-mask-title">{{ offlineTitle }}</h2>

    <p class="offline-mask-desc">{{ desc }}</p >
  </div>
</template>

<script>
export default {
  name: "offline-handle",
  props: {
    offlineTitle: {
      type: String,
      default: "网络已断开，请检查网络连接。",
    },
    onlineTitle: {
      type: String,
      default: "网络已连接",
    },
    desc: {
      type: String,
      default: "",
    },
    duration: {
      type: Number,
      default: 4.5,
    },
  },
  data() {
    return {
      mask: false,
    };
  },
  mounted() {
    window.addEventListener("offline", this.eventHandle);
    window.addEventListener("online", this.eventHandle);
    console.log(this.desc);
  },
  beforeDestroy() {
    window.removeEventListener("offline", this.eventHandle);
    window.removeEventListener("online", this.eventHandle);
  },
  methods: {
    eventHandle(event) {
      const type = event.type === "offline" ? "error" : "success";
      this.$Notice[type]({
        title: type === "error" ? this.offlineTitle : this.onlineTitle,
        desc: type === "error" ? this.desc : "",
        duration: this.duration,
      });
      setTimeout(() => {
        this.mask = event.type === "offline";
      }, 1500);
    },
  },
};
</script>
<style lang="css" scoped>
.offline-mask {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: #ccc;
  z-index: 9999;
  transition: position 2s;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
}
.offline-mask-title {
  color: rgba(0, 0, 0, 0.8);
}
.offline-mask-desc {
  margin-top: 20px;
  color: red;
  font-weight: bold;
}
</style>
```