## 复制表单元素内容
```js
# 方法一
  // TODO 原生复制功能
  const { addAccountInfo } = this.props
  const _input = document.createElement('input')
  const _content = `用户名: ${addAccountInfo.username},初始密码:${addAccountInfo.password}`
  _input.setAttribute('value', _content)
  document.body.appendChild(_input)
  _input.select()
  const result = document.execCommand('copy')
  document.body.removeChild(_input)
  if (addAccountInfo) {
    TopTips({
      theme: 'done',
      message: '已复制'
    })
  }
  return result
}
# 方法二
import copy from 'copy-to-clipboard'
// 使用copy-to-clipboard 包 来复制内容
handleCopy = () => {
  const { addAccountInfo } = this.props
  const _content = `用户名: ${addAccountInfo.username},初始密码:${addAccountInfo.password}`
  copy(_content)
  if (addAccountInfo) {
    TopTips({
      theme: 'done',
      message: '已复制'
    })
  }
}
```

## 复制带换行符的文本
```js
// 点击 复制按钮
handleCopy = (data) => {
  // input不支持换行，所以使用textarea来实现
  const _textarea = document.createElement('textarea')
  // 替换;
  const str = data.replace(/;/g, '\n')
  _textarea.value = str
  document.body.appendChild(_textarea)
  _textarea.select()
  const result = document.execCommand('copy')
  document.body.removeChild(_textarea)
  if (data) {
    TopTips({
      theme: 'done',
      message: '已复制'
    })
  }
  return result
}
```