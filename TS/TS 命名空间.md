[TOC]
## 命名空间 namespace
```javascript
// page.js
/// <reference path="components.ts" />

namespace Home {
  export class Page {
    constructor() {
      // Home的命名空间需要引用Components命名空间,在文件开头使用reference注明依赖
      new Components.Header();
      new Components.Content();
      new Components.Footer();
    }
  }
}

// components.ts
namespace Components {
  // 子命名空间
  export namespace SubComponents {
    export class Test {
      constructor() {
        console.log("test");
      }
    }
  }
  export class Header {
    constructor() {
      const ele = document.createElement("div");
      ele.innerText = "This is Header";
      document.body.appendChild(ele);
    }
  }
  export class Content {
    constructor() {
      const ele = document.createElement("div");
      ele.innerText = "This is Content";
      document.body.appendChild(ele);
    }
  }
  // 导出
  export class Footer {
    constructor() {
      const ele = document.createElement("div");
      ele.innerText = "This is Footer";
      document.body.appendChild(ele);
    }
  }
}

```

