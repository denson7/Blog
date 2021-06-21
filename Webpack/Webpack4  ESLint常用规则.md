```javascript
module.exports = {
  root: true,
  env: {
    // browser 的全局变量
    'browser': true,
    // Node.js 全局变量和 Node.js 作用域
    'node': true,
    // 支持除模块外所有 ECMAScript 6 特性
    'es6': true,
    // 支持单元测试jest
    // jest: true
  },
  extends: [
    "plugin:vue/vue3-essential",
    "@vue/standard",
    "@vue/typescript/recommended"
  ],
  parserOptions: {
    ecmaVersion: 2020
  },
  rules: {
    // 0 关闭 1 警告 2 开启规则
    // 圈复杂度
    "complexity": [2, { max: 20 }],
    // 双峰驼命名格式
    "camelcase": 2,
    // 强制使用一致的换行风格
    "linebreak-style": [1, "unix"],
    // 每行最大长度
    "max-len": [2, { code: 120, ignoreComents: false, ignoreTrilingComents: true, ignoreTemplateLiterals: true }],
    // 结尾分号
    "semi": [2, "always"],
    // 强制使用一致的反勾号、双引号或单引号
    "quotes": [2, "single", "avoid-escape"],
    // 缩进2个空格
    "indent": [2, 2, { "SwitchCase": 1 }],
    // 禁止或强制在单行代码块中使用空格(禁用)
    "block-spacing": [1, "always"],
    // 箭头函数，一个参数可省略括号
    "arrow-parens": [2, "as-needed"],
    // 箭头函数，箭头前后需留空格
    "arrow-spacing": [2, { before: true, after: true }],
    // 不使用尾部逗号
    "comma-dangle": [2, "never"],
    // 逗号前面无空格，尾部有空格
    "comma-spacing": [2, { before: false, after: true }],
    // 未使用的变量
    "no-unused-vars": 2,
    // 强制在对象字面量的属性中键和值之间使用一致的间距
    "key-spacing": [2, { beforeColon: false, afterColon: true }],
    // 以方括号取对象属性时，不加空格
    "computed-property-spacing": [2, "never"],
    // 要求构造函数首字母大写
    "new-cap": [2, { "newIsCap": true, "capIsNew": false }],
    // 调用构造函数，圆括号不能省略
    "new-parens": 2,
    // 回调函数最大嵌套深度4层
    "max-nested-callbacks": [2, { max: 4 }],
    // 不允许使用嵌套的三元表达式
    "no-nested-ternary": 2,
    // 禁止使用多行字符串 \
    "no-multi-str": 2,
    // 禁止使用多个空格
    "no-multi-spaces": 2,
    // 强制标识符的最新和最大长度
    "id-length": [2, { min: 1, max: 20, properties: "never" }],
    // 单行注释加空格
    "space-comment": [2, "always"],
    // 函数最多5个参数, 警告
    "max-params": [1, 5],
    // if(){} 不能省略{}
    "curly": [2, "always"],
    // 要求在注释周围有空行 ( 要求在块级注释之前有一空行)
    "lines-around-comment": [1, { "beforeBlockComment": true }],
    // 强制在function的左括号之前使用一致的空格
    "space-before-function-paren": [2, { anonymous: "always", named: "never", asyncArrow: "always" }],
    // 禁止在注释中使用特定的警告术语
    "no-warning-comments": [2, { terms: ["todo", "fixme", "any other term"], location: "anywhere" }],
    // 禁止一次声明多个变量
    "one-var": [2, { var: "never", let: "never", const: "never" }],
    // 禁止不必要的return await
    "no-return-await": 2,
    // switch应有default语句
    "default-case": 2,
    // 变量声明应尽快初始化
    "init-declarations": 1,
    // 嵌套升读不超过4层
    "max-depth": [2, { max: 4 }],
    // 尽量使用对象字面量中方法和属性使用简写语法
    "object-shorthand": 2,
    // 强制在圆括号内使用一致的空格
    "space-in-parens": [2, "never"],
    // 操作符前后加空格
    "space-infix-ops": 2,
    // 强制在花括号中使用一致的空格
    "object-curly-spacing": [1, "always"],
    // 优先使用点符号访问对象属性
    "dot-notation": [2, { "allowKeywords": false }],
    // 使用 === 替代 == 允许null和undefined ==
    "eqeqeq": [2, "allow-null"],
    // 设置大括号的换行风格，不检查
    'object-curly-newline': [0],
    // 函数调用时，函数名与()之间不要空格
    "no-spaced-func": 2,
    // 禁止对象字面量中出现重复的 key
    "no-dupe-keys": 2,
    // 禁止重复的 case 标签
    "no-duplicate-case": 2,
    // 禁止正则表达式字面量中出现多个空格
    "no-regex-spaces": 2,
    // 禁用 eval()
    "no-eval": 2,
    // 禁止使用类似 eval() 的方法
    "no-implied-eval": 2,
    // 禁用 with 语句
    "no-with": 2,
    // 禁用不必要的转义字符
    "no-useless-escape": 2,
    // 禁止将变量初始化为 undefined
    "no-undef-init": 2,
    // 禁止将 undefined 作为标识符
    "no-undefined": 2,
    // 禁用 continue 语句
    "no-continue": 0,
    // 禁用行尾空格
    "no-trailing-spaces": 2,
    // 禁止属性前有空白
    "no-whitespace-before-property": 0,
    // 禁止重复导入模块
    "no-duplicate-imports": 2,
    // 禁止修改 const 声明的变量
    "no-const-assign": 2,
    // 禁止在构造函数中，在调用 super() 之前使用 this 或 super
    "no-this-before-super": 2,
    // 建议是用rest参数
    "prefer-rest-params": 1,
    // 优先使用变量解构申明，警告
    "prefer-destructuring": 1,
    // 建议使用扩展运算符
    "prefer-spread": 1,
    // 禁止混合使用不同的操作符
    "no-mixed-operators": 1,
    // _使用悬空的下划线命名，允许
    "no-underscore-dangle": [0],
    // 对函数参数再赋值，警告
    "no-param-reassign": [1],
    // 使用++/--操作符，允许
    "no-plusplus": [0],
    // url中使用javascript:;，警告
    "no-script-url": [1],
    // 禁止的表达式，允许简写
    "no-unused-expressions": [2, { "allowShortCircuit": true }],

    // node声明变量需top全局，与动态加载冲突，警告
    "global-require": [1],
    // 类方法没用到this，不检查
    "class-methods-use-this": [0],
    // 一致的返回值，警告
    "consistent-return": [1],
    // node需要动态加载，警告
    "import/no-dynamic-require": [1],
    // 禁止使用本项目没有依赖的包，<与别名冲突>不检查
    "import/no-extraneous-dependencies": [0],
    // 补全import路径的文件后缀，不检查
    "import/extensions": [0],
    // 要求相对路径模块存在，<与别名冲突>不检查
    "import/no-unresolved": [0],
    // 要求module使用default export，不检查
    "import/prefer-default-export": [0],
    // .js后缀的文件可以包含JSX代码，统一js后缀
    "react/jsx-filename-extension": [1, { extensions: [".js"] }],
    // 无状态组件应为纯函数，警告
    "react/prefer-stateless-function": [1],
    // React组件拥有props校验，不检查
    "react/prop-types": [0],
    // React组件方法有序，警告
    // https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/sort-comp.md
    "react/sort-comp": [1],
    // 优先使用变量解构申明，警告
    "react/destructuring-assignment": [1],
    // 禁止使用不推荐的propTypes，警告
    "react/forbid-prop-types": [1],
    // 禁止使用数组下标作为组件的key值，警告
    "react/no-array-index-key": [1],
    // jsx里一行一个表达式，不检查
    "react/jsx-one-expression-per-line": [0],
    // jsx里a标签 target 需配合 ref，不检查
    "react/jsx-no-target-blank": [0],
    // button必须指明type，可选值"button", "submit", "reset"。
    // type默认值为submit，指明type可避免错误的行为。警告
    "react/button-has-type": [1],
    // 要求label标签通过htmlFor指向实际关联的标签，警告
    "jsx-a11y/label-has-for": [1],
    // 要求label标签必须配合input，警告
    "jsx-a11y/label-has-associated-control": [1],
    // 非交互元素应含属性role标记，不检查
    "jsx-a11y/no-static-element-interactions": [0],
    "jsx-a11y/no-noninteractive-element-interactions": [0],
    // 除鼠标外，应同时支持键盘操作，不检查
    "jsx-a11y/click-events-have-key-events": [0],
    // 正确使用a标签，如非链接应该使用button，不检查
    "jsx-a11y/anchor-is-valid": [0],
  }
};

```
```javascript
https://github.com/yuche/javascript
airbnb-javascript：包含了airbnb默认的JavaScript规范，有简单的注释。
.eslintignore：设置哪些文件不进行eslint检测，类似于.gitignore，请拷贝到项目的根目录
.eslintrc.js：自定义的eslint规则，部分规则为了照顾老项目，适当放宽。请拷贝到项目的根目录
eslint.sh：该脚本完成只对发生变化的文件进行eslint检测功能，请拷贝到项目中合适的位置
{
  "scripts": {
    "eslint": "chmod +x scripts/* && scripts/eslint.sh"
  },
  // pre-commit钩子
  "pre-commit": [
    "eslint"
  ],
  // eslint相关的依赖
  "devDependencies": {
    "babel-eslint": "^8.2.2",
    "eslint": "^4.16.0",
    "eslint-config-airbnb": "^16.1.0",
    "eslint-plugin-import": "^2.8.0",
    "eslint-plugin-jsx-a11y": "^6.0.3",
    "eslint-plugin-react": "^7.6.0",
  }
}

```

