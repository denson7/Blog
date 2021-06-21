[TOC]
## Prettier
`Prettier`是一个支持多种文件格式（包括javascript、css、less、sass、typescript、markdown等）的样式格式化工具。它会删除代码里的原始样式，并确保输出的代码风格一致。

## 安装

```javascript
npm install --save-dev prettier
#全局安装
npm install --global prettier
```
## 使用

```javascript
#方法1：使用.prettierrc文件
{
  "printWidth": 120,              // 单行最大字符数
  "semi": true,                    // 句末加分号
  "tabWidth":2,                    // 缩进长度为2,
  "singleQuote": true,            // 所有引号均使用单引号
  "trailingComma": "none",        // 最后一个对象元素加逗号
  "bracketSpacing": true,          // 对象，数组加空格
  "jsxBracketSameLine": false,    // jsx > 是否另起一行
  "arrowParens": "avoid",          // (x) => {} 是否要有小括号
  "requirePragma": false,          // 是否要注释来决定是否格式化代码
  "proseWrap": "preserve"          // 是否要换行
}
#方法2：使用prettier.config.js 文件
#方法3：package.json 中配置prettier属性
{ 
  "scripts": { 
    "format": "prettier --write '**/*.{js,css,md}'"
  }
}
```

## 搭配ESlinit使用
### 安装

```
npm install prettier eslint-plugin-prettier --save-dev
```
```javascript
#package.json
{
  "scripts": {
    "lint": "eslint *.js"
  },
  "eslintConfig": {
    "extends": [
      "prettier"
    ],
    "plugins": [
      "prettier"
    ],
    "rules": {
      "prettier/prettier": "error"
    }
  },
  "prettier": {
    "printWidth": 120, 
      "semi": true,                  
      "tabWidth":2,                  
      "singleQuote": true,      
      "trailingComma": "none",      
      "bracketSpacing": true,      
      "jsxBracketSameLine": false,
      "arrowParens": "avoid",          
      "requirePragma": false,        
      "proseWrap": "preserve"          
  }
}
```
### 配置

```javascript
#.eslintrc
{
  "plugins": ["prettier"],
  "rules": {
    "prettier/prettier": [
      "error",
      {
        "printWidth": 100,
        "singleQuote": true,
        "trailingComma": "es5"
      }
    ]
  }
}
```
