[TOC]
## tsconfig.json说明
```javascript
// 查看官网文档
http://www.typescriptlang.org/docs/handbook/tsconfig-json.html
// compilerOptions参数
http://www.typescriptlang.org/docs/handbook/compiler-options.html
```
```javascript
// tsc 默认时编译当前目录下所有的文件，在tsconfig.json中include, exclude指定文件编译
```
### 常用配置
```json
{
  // 编译的选项
  "compilerOptions": {
    // 指定模块生成方式 ["commonjs", "amd", "umd", "system", "es6", "es2015", "esnext", "none"]
    "module": "commonjs",
    // 指定要包含在编译中的库文件
    "lib": ["es6", "dom"],
    // 输出代码 ES 版本
    "target": "es2015",
    // 是否在编译时生成 .d.ts 声明文件
    "declaration": true,
    // 是否将编译后的文件中的注释删掉，默认为false
    "removeComments": true,
    // 是否启用实验性的装饰器特性
    "emitDecoratorMetadata": true,
    // 是否为装饰器提供元数据支持
    "experimentalDecorators": true,
    // 是否在编译时生成对应的 map 文件
    "sourceMap": true,
    // 定义输出文件的文件夹
    "outDir": "./dist",
    // 定义模块查找的路径
    "baseUrl": "./",
    // 阻止--noImplicitAny对缺少索引签名的索引对象报错， 默认为false, 可以动态添加对象属性
    "suppressImplicitAnyIndexErrors": true,
    // 阻止对对象字面量的额外属性检查，默认为false
    "suppressExcessPropertyErrors": true,
    // 是否启用增量编译
    "incremental": true
  },
  // 检测目录
  "include": [
    "src/*"
  ],
  // 排除目录
  "exclude": [
    "node_modules",
    "dist"
  ]
}

```

### 配置项说明
```json
{
  "compilerOptions": {
    /* Basic Options */
    // 指定编译之后的版本目标: 'ES3' (default), 'ES5', 'ES2015', 'ES2016', 'ES2017', 'ES2018', 'ES2019' or 'ESNEXT'.
    "target": "es5",
    // 指定要使用的模块标准: 'none', 'commonjs', 'amd', 'system', 'umd', 'es2015', or 'ESNext'
    "module": "commonjs",
    // 指定要包含在编译中的库文件
    "lib": [
      "es6",
      "dom"
    ],
    // 阻止--noImplicitAny对缺少索引签名的索引对象报错， 默认为false, 可以动态添加对象属性
    "suppressImplicitAnyIndexErrors": true,
    // 阻止对对象字面量的额外属性检查，默认为false
    "suppressExcessPropertyErrors": true,

    // 指定是否允许编译js文件，默认是false，即不编译js文件
    "allowJs": true,
    // 不报告未使用的标签错误， 默认为false
    "allowUnusedLabels": true,
    // 指定是否检查和报告js文件中的错误，默认是false
    "checkJs": true,
    // 指定jsx代码用于的开发环境: 'preserve', 'react-native', or 'react'.
    "jsx": "preserve",

    // 指定是否在编译时生成相应的".d.ts"声明文件。如果设为true，编译每个ts文件都会生成一个js文件和一个声明文件。
    // 注意：declaration和allowJs不能同时设为true
    "declaration": true,
    // 指定是否为声明文件.d.ts生成map文件
    "declarationMap": true,
    // 指定编译时是否生成.map文件
    "sourceMap": true,
    // 指定将输出文件合并为一个文件，它的值为一个文件路径名。比如设为"./dist/main.js"，则输出的文件为一个main.js文件。但要注意，只有设置module的值为amd和system模块时才支持这个配置
    "outFile": "./",
    // 指定输出文件夹，值为一个文件夹路径字符串，输出的文件都将放置在这个文件夹
    "outDir": "./dist",
    // 指定编译文件的根目录，编译器会在根目录查找入口文件
    "rootDir": "./",
    // 是否编译构建引用项目
    "composite": true,
    // 是否启用增量编译
    "incremental": true,
    // 指定文件以存储增量编译信息
    "tsBuildInfoFile": "./",
    // 指定是否将编译后的文件中的注释删掉，默认为false
    "removeComments": true,
    // 不生成编译文件，这个一般比较少用
    "noEmit": true,
    // 指定是否引入tslib里的辅助工具函数，默认为false
    "importHelpers": true,
    // 当target为'ES5' or 'ES3'时，为'for-of', spread, and destructuring'中的迭代器提供完全支持
    "downlevelIteration": true,
    // 指定是否将每个文件作为单独的模块，默认为true，它不可以和declaration同时设定
    "isolatedModules": true,

    /* Strict Type-Checking Options */
    // 指定是否启动所有类型检查，如果设为true则会同时开启下面这几个严格类型检查，默认为false
    "strict": true,
    // 在表达式和声明上有隐含的any类型时报错, 默认值为false
    "noImplicitAny": true,
    // strictNullChecks为true时，null和undefined值不能赋给非这两种类型的值，别的类型也不能赋给他们，除了any类型。还有个例外就是undefined可以赋值给void类型
    "strictNullChecks": true,
    // 指定是否使用函数参数双向协变检查
    "strictFunctionTypes": true,
    // 设为true后会对bind、call和apply绑定的方法的参数的检测是严格检测的
    "strictBindCallApply": true,
    // 设为true后会检查类的非undefined属性是否已经在构造函数里初始化，如果要开启这项，需要同时开启strictNullChecks，默认为false
    "strictPropertyInitialization": true,
    // 当this表达式的值为any类型的时候，生成一个错误
    "noImplicitThis": true,
    // 指定始终以严格模式检查每个模块，并且在编译之后的js文件中加入"use strict"字符串，用来告诉浏览器该js为严格模式
    "alwaysStrict": true,
    /* Additional Checks */
    // 检查是否有定义了但是没有使用的变量，默认为false
    "noUnusedLocals": true,
    // 检查是否有在函数体中没有使用的参数，默认为false
    "noUnusedParameters": true,
    // 检查函数是否有返回值，默认为false
    "noImplicitReturns": true,
    // 检查switch中是否有case没有使用break跳出switch，默认为false
    "noFallthroughCasesInSwitch": true,

    /* Module Resolution Options */
    // 用于选择模块解析策略，有'node'和'classic'两种类型，默认classic
    "moduleResolution": "node",
    // 控制台输出模块解析日志信息，默认为false
    "traceResolution": false,
    // 显示诊断信息，默认为false
    "diagnostics": false,
    // 在编译过程中打印文件名，默认为false
    "listFiles": false,
    // 打印出编译后生成文件名，默认为false
    "listEmittedFiles": false,
    // 给错误和消息设置样式，使用颜色和上下文， 默认为false
    "pretty": false,
    // 显示错误信息时使用的语言
    "locale": "en-us",
    // 模块输出中不包含"use strict"指令，默认false
    "noImplicitUseStrict": false,

    // 忽略所有的声明文件（*.d.ts）的类型检查，默认为false
    "skipLibCheck": true,

    // 设置解析非相对模块名称的基本目录，相对模块不会受baseUrl的影响
    "baseUrl": "./",
    // 设置模块名称到基于baseUrl的路径映射
    "paths": {},
    // 指定一个路径列表，在构建时编译器会将这个路径列表中的路径的内容都放到一个文件夹中
    "rootDirs": [],
    // 指定声明文件或文件夹的路径列表，如果指定了此项，则只有在这里列出的声明文件才会被加载
    "typeRoots": [],
    // 指定需要包含的模块，只有在这里列出的模块的声明文件才会被加载进来
    "types": [],
    // 指定允许从没有默认导出的模块中默认导入
    "allowSyntheticDefaultImports": true,
    // 通过为导入内容创建命名空间，实现CommonJS和ES模块之间的互操作性
    "esModuleInterop": true,
    // 不把符号链接解析为其真实路径，具体可以了解下webpack和nodejs的symlink相关知识
    "preserveSymlinks": true,
    /* Source Map Options */
    // 指定调试器应该找到TypeScript文件而不是源文件位置，这个值会被写进.map文件里
    "sourceRoot": "",
    // 指定调试器找到映射文件而非生成文件的位置，指定map文件的根路径，该选项会影响.map文件中的sources属性
    "mapRoot": "",
    // 指定是否将map文件的内容和js文件编译在同一个js文件中，如果为true，则map的内容会以//# sourceMappingURL=然后拼接base64字符串的形式插入在js文件底部
    "inlineSourceMap": true,
    // 指定是否进一步将.ts文件的内容也包含到输入文件中
    "inlineSources": true,

    /* Experimental Options */
    // 指定是否启用实验性的装饰器特性，默认false
    "experimentalDecorators": true,
    // 指定是否为装饰器提供元数据支持，默认false
    "emitDecoratorMetadata": true
  },
  // 指定文件的相对或绝对路径，编译器在编译的时候只会编译包含在files中列出的文件，如果不指定，则取决于有没有设置include选项，如果没有include选项，则默认会编译根目录以及所有子目录中的文件。这里列出的路径必须是指定文件，而不是某个文件夹，而且不能使用* ? **/ 等通配符
  "files": [],
  // 指定要编译的路径列表
  // 和files的区别在于，这里的路径可以是文件夹，也可以是文件，可以使用相对和绝对路径，而且可以使用通配符，比如"./src"即表示要编译src文件夹下的所有文件以及子文件夹的文件
  "include": [
    "src/*"
  ],
  // 指定要排除的、不编译的文件，可以是一个列表，规则和include一样，可以是文件或文件夹，可以是相对路径或绝对路径，可以使用通配符
  "exclude": [
    "node_modules",
    "dist"
  ],
  // 指定一个其他的tsconfig.json文件路径，来继承这个配置文件里的配置，继承来的文件的配置会覆盖当前文件定义的配置
  "extends": "",
  // compileOnSave如果设为true，在我们编辑了项目中的文件保存的时候，编辑器会根据tsconfig.json中的配置重新生成文件，不过这个要编辑器支持
  "compileOnSave": true,
  // 指定要引用的项目
  "references": []
}

```
#### 解决 import * as xxx from 'xxx' 这种奇怪的引入方式
```javascript
// 配置
{
  // 允许 默认导入 没有设置默认导出（export default xxx）的模块
  // 可以以 import xxx from 'xxx' 的形式来引入模块
    "allowSyntheticDefaultImports":true
}
// 配置前
import * as React from 'react';
import * as ReactDOM from 'react-dom';

// 配置后
import React from 'react';
import ReactDOM from 'react-dom';

```

