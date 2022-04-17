[TOC]
## tslint 命令参数
```javascript
"tslint": "tslint --fix  . ./src/**/*.ts"
```
```javascript
// 使用
tslint [options] [file...]
// 参数
-v, --version                          output the version number

-c, --config [config]                  configuration file

-e, --exclude <exclude>                exclude globs from path expansion

--fix                                  fixes linting errors for select rules (this may overwrite linted files)

--force                                return status code 0 even if there are lint errors

-i, --init                             generate a tslint.json config file in the current working directory

-o, --out [out]                        output file

--outputAbsolutePaths                  whether or not outputted file paths are absolute

--print-config                         print resolved configuration for a file

-r, --rules-dir [rules-dir]            rules directory

-s, --formatters-dir [formatters-dir]  formatters directory

-t, --format [format]                  output format (prose, json, stylish, verbose, pmd, msbuild, checkstyle, vso, fileslist, codeFrame)

-q, --quiet                            hide non "error" severity linting errors from output

--test                                 test that tslint produces the correct output for the specified directory

-p, --project [project]                tsconfig.json file

--type-check                           (deprecated) check for type errors before linting the project

-h, --help                             output usage information

```
## tslint规则说明
```json
{
  "defaultSeverity": "error",
  "extends": [
    "tslint:recommended"
  ],
  "jsRules": {
    "no-unused-expression": true
  },
  "rules": {
    // 必须使用单引号
    "quotemark": [true, "single"],
    // 是否显式声明 class 的成员(public,protected,private)
    "member-access": [false],
    // 是否要求class内成员的声明顺序
    "member-ordering": [false],
    // 是否要求导入语句按字母顺序排列和分组
    "ordered-imports": [false],
    // 设置单行最大字数
    "max-line-length": [true, 150],
    // 是否要求接口名称以大写字母"I"开头
    "interface-name": [false],
    // 是否要求箭头函数的参数加括号
    "arrow-parens": false,
    // 是否检查对象 key 的声明顺序
    "object-literal-sort-keys": false,
    // 设置代码缩进为2个空格
    "indent": [true, "spaces", 2],
    // 是否禁用按位运算符(true表示不允许,false表示允许)
    "no-bitwise": false,
    // 设置对尾随逗号的校验
    "trailing-comma": [
      true,
      {
        // 检查多行对象文本
        "multiline": {
          "objects": "always",
          "arrays": "always",
          "functions": "never",
          "typeLiterals": "ignore"
        },
        // 是否允许尾随逗号出现在剩余变量中
        "esSpecCompliant": true
      }
    ],
    // ----------------------------------------
    // ---------------Dev-----------------------
    // 不能使用console
    "no-console": false,
    // ----------------------------------------
    // ----------------------------------------
    // ----------------------------------------
    // ----------------------------------------
    // -----------------ADD--------------------
    // ----------------------------------------
    // ----------------------------------------
    // 强制单行注释格式:// 后必跟空格
    "comment-format": [
      true,
      "check-space"
    ],
    // 任何情况下，if / for / do / while 后必须通过 {} 包裹代码块
    "curly": true,
    // 文件必须用新的行结束
    "eofline": true,
    // 当使用 for in 语句遍历 object 时，强制使用 if 判断 key 是否由原型链继承 hasOwnProperty
    "forin": true,
    // 禁止数组中的模块全量引入，如：rxjs，需要导入其具体子模块
    "import-blacklist": [
      true,
      "rxjs/Rx"
    ],
    // import { ModuleName } 确保 ModuleName 两端留有空格
    "import-spacing": true,
    // 声明 interface 接口，代替 type 关键字声明类型
    "interface-over-type-literal": true,
    // ？label 是个什么东西？只允许 labels 在 do / for / while / switch 的声明中
    "label-position": true,
    // 一个文件最多定义一个 class，保证单一职责
    "max-classes-per-file": [
      true,
      1
    ],
    // 必须使用箭头函数，除非是单独的函数声明或是命名函数
    "only-arrow-functions": [
      false,
      "allow-declarations",
      "allow-named-functions"
    ],
    // 禁止对 array 使用 for in 循环
    "no-for-in-array": false,
    // 使用 return; 而不是 return undefined;
    "return-undefined": false,
    // 不允许使用 arguments.callee
    "no-arg": true,
    // 不许用 new Number() String Boolean 等基础类型的构造函数，允许使用函数式调用 Number(<string>)
    // More details: https://stackoverflow.com/questions/4719320/new-number-vs-number
    "no-construct": true,
    // 不许用 debugger
    "no-debugger": true,
    // 不允许两次调用 super 函数
    "no-duplicate-super": true,
    // 不许有空代码块 {}, 但允许 catch 是空代码块
    "no-empty": [
      false,
      "allow-empty-catch"
    ],
    // 不许有空接口
    "no-empty-interface": true,
    // 不许用 eval 函数
    "no-eval": true,
    // 声明立即被赋值的基础类型不需要声明变量类型，let state: boolean = false 删除类型声明
    "no-inferrable-types": [
      true,
      "ignore-params"
    ],
    // 不要为 interface 定义 constructor 函数
    "no-misused-new": true,
    // Disallows non-null assertions using the ! postfix operator.
    // 不许使用 params1!.func() 的语法校验参数值是否存在
    "no-non-null-assertion": true,
    // 不允许子作用域与外层作用域声明同名变量，
    // (instance, instances) => {return instances.filter((instance)=>instance.neighbors.includes(instance))}
    // Disallows shadowing variable declarations.
    "no-shadowed-variable": true,
    // 推荐使用 obj.property 代替 obj["property"]，但是 obj["prop-erty"] 是被允许的
    "no-string-literal": false,
    // 不允许直接 throw 一个字符串字面量
    // if (!productToAdd) {
    // throw new Error("How can I add new product when no value provided?");
    // }
    "no-string-throw": true,
    // 代码行尾不允许有空格
    "no-trailing-whitespace": true,
    // 不允许做 var|let unn = undefined 这种无意义赋值
    "no-unnecessary-initializer": true,
    // 禁止无用的表达式
    "no-unused-expression": true,
    // 不许使用 var 声明变量
    "no-var-keyword": true,
    // 禁止对函数的参数重新赋值
    "no-parameter-reassignment": true,
    // 禁止在 switch 语句中出现重复测试表达式的 case
    "no-duplicate-switch-case": true,
    // switch 的 case 必须 return 或 break
    "no-switch-case-fall-through": true,
    // 禁止出现重复的变量定义或函数参数名
    "no-duplicate-variable": [
      true,
      "check-parameters"
    ],
    // 禁止没必要的 return await
    "no-return-await": true,
    // 禁止出现重复的 import
    "no-duplicate-imports": true,
    // 禁止一个文件中出现多个相同的 namespace
    "no-mergeable-namespace": true,
    // 禁止使用特殊空白符（比如全角空格）
    "no-irregular-whitespace": true,
    // 小数必须以 0. 开头，禁止以 . 开头，并且不能以 0 结尾
    "number-literal-format": true,
    // 文件类型必须时 utf-8
    "encoding": true,
    // if 后的 { 禁止换行
    "one-line": true,
    // 优先使用 const 声明变量
    "prefer-const": true,
    // 使用 parseInt 时需要定义 radix 参数
    "radix": true,
    // 结尾必须要分号，即每个声明后强制 ;
    "semicolon": [
      true,
      "always"
    ],
    // 强制用 === 替换 ==
    "triple-equals": [
      true,
      "allow-null-check"
    ],
    // 变量申明必须每行一个，for 循环的初始条件中除外
    "one-variable-per-declaration": [
      true,
      "ignore-for-loop"
    ],
     // 必须使用 a = {b} 而不是 a = {b: b}
    "object-literal-shorthand": true,
    // 函数名前必须有空格
    "space-before-function-paren": true,
    // 冒号左侧的空格数量，下面的列表中全部不要空格，
    // "call-signature" checks return type of functions.
    // "index-signature" checks index type specifier of indexers.
    // "parameter" checks function parameters.
    // "property-declaration" checks object property declarations.
    // "variable-declaration" checks variable declaration.
    "typedef-whitespace": [
      true,
      {
        "call-signature": "nospace",
        "index-signature": "nospace",
        "parameter": "nospace",
        "property-declaration": "nospace",
        "variable-declaration": "nospace"
      }
    ],
    // 校验两个重载函数是否可以通过 ;;rest 参数合并 (...params) => arguments
    "unified-signatures": true,
    // 校验变量名起的有没有问题，大小驼峰命名，过滤保留关键字
    "variable-name": false,
    // new 后面只必须有一个空格
    "new-parens": true,
    //? "variable-name": [true, "ban-keywords", "check-format", "allow-leading-underscore"],
    // 强制空格用法，
    // if else for while 后面接一个空格
    // 变量赋值的 = 两侧加空格
    // 运算符两侧加空格
    // 分隔符后加空格，<,|/|;>
    // 变量类型声明前加空格
    "whitespace": [
      true,
      "check-branch",
      "check-decl",
      "check-operator",
      "check-separator",
      "check-type"
    ]
  },
  "rulesDirectory": []
}

```
## tslint注释标记
```javascript
/* tslint:disable */——忽略该行以下所有代码出现的错误提示
/* tslint:enable */——当前ts文件重新启用tslint
// tslint:disable-line——忽略当前行代码出现的错误提示
// tslint:disable-next-line——忽略下一行代码出现的错误提示
```

