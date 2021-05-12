## 多条件语句
```javascript
// if else
function pick(color) {
  if (color === "red") {
    return ["apple", "strawberry"];
  } else if (color === "yellow") {
    return ["banana", "pineapple"];
  } else if (color === "purple") {
    return ["grape", "plum"];
  } else {
    return [];
  }
}

// switch case
function pick2(color) {
  // 根据颜色选择水果
  switch (color) {
    case "red":
      return ["apple", "strawberry"];
    case "yellow":
      return ["banana", "pineapple"];
    case "purple":
      return ["grape", "plum"];
    default:
      return [];
  }
}

// Object 的 { key: value } 结构
const fruitColor3 = {
  red: ['apple', 'strawberry'],
  yellow: ['banana', 'pineapple'],
  purple: ['grape', 'plum'],
};

function pick3(color) {
  return fruitColor3[color] || [];
}

// 使用 Map 数据结构，真正的 (key, value) 键值对结构
const fruitColor4 = new Map()
  .set('red', ['apple', 'strawberry'])
  .set('yellow', ['banana', 'pineapple'])
  .set('purple', ['grape', 'plum']);

function pick4(color) {
  return fruitColor4.get(color) || [];
}
```

### 策略模式优化
```javascript
// 策略模式
const TYPE = {
  JUICE: 'juice',
  SALAD: 'salad',
  JAM: 'jam',
};

const strategies = {
  [TYPE.JUICE](fruits) {
    console.log('榨果汁中...');
    return '果汁';
  },
  [TYPE.SALAD](fruits) {
    console.log('做沙拉中...');
    return '沙拉';
  },
  [TYPE.JAM](fruits) {
    console.log('做果酱中...');
    return '果酱';
  },
};

function enjoy2({ type = TYPE.JUICE, fruits }) {
  if (!type) {
    console.log('请直接享用！');
    return;
  }
  if (!fruits || !fruits.length) {
    console.log('请先采购水果！');
    return;
  }
  return strategies[type](fruits);
}

enjoy2({ type: 'juice', fruits });
```

```typescript
if (status == 0) {
  // 业务逻辑处理 0
} else if (status == 1) {
  // 业务逻辑处理 1
} else if (status == 2) {
  // 业务逻辑处理 2
} else if (status == 3) {
  // 业务逻辑处理 3
}
// 多态
interface A {
  void run() throws Exception;
}

class A0 implements A {
    @Override
    void run() throws Exception {
        // 业务逻辑处理 0
    }
}

class A1 implements A {
    @Override
    void run() throws Exception {
        // 业务逻辑处理 1
    }
}
// 然后策略对象存放在一个 Map 中
A a = map.get(param);
a.run();
```
```javascript
const obj = {
  A: (num) => num * 4,
  B: (num) => num * 6,
  C: (num) => num * 8,
};

const getSum = function (type, num) {
  return obj[type](num);
};
```