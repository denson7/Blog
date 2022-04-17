在 tsconfig.json 中可以设置 `checkJs:true`，对 .js 文件进行类型检查和错误提示。
```javascript
通过在 .js 文件顶部添加 // @ts-nocheck 注释，让编译器忽略当前文件的类型检查。
相反，你可以通过不设置 checkJs:true 并在 .js 文件顶部添加一个 // @ts-check 注释，让编译器检查当前文件。
也可以在 tsconfig.json 中配置 include/exclude，选择/排除对某些文件进行类型检查 。
你还可以使用 // @ts-ignore 来忽略本行的错误。
```

