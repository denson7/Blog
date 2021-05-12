## 多个空格替换为一个空格
```javascript
export const escape = (str) => str.replace(/\s+/g, " ");
```
## 替换手机号码中间4位为*
```javascript
/**
 * 将手机号中间部分替换为星号
 * @param phone{string}: 手机号码
 */
export const formatPhoneNumber = (phone) => {
  return phone.replace(/(\d{3})\d{4}(\d{4})/, "$1****$2");
};
```
##仅允许字母数字
```javascript
export const alphaNumeric = value => (value && !/[^a-zA-Z0-9 ]/i.test(value)
  ? true
  : '只能使用数字和英文字母')
```
##仅允许字母数字中文
```javascript
export const alphaNumericZH = value => (value && /[^\u4e00-\u9fa5a-zA-Z0-9_-]/g.test(value)
  ? '只允许包含中文、字母、数字、-、_'
  : undefined)
```
##必须包含数字、英文和字符至少三种类型

##验证邮箱
```javascript
export const isEmail = value => (value && /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,4}$/i.test(value)
  ? true
  : '无效的邮箱地址')
```
##验证电话号码
```javascript
// 验证手机号码
export const isPhoneNumber = (value) => !(value && /^((13[0-9])|(14[5|7])|(15([0-3]|[5-9]))|(18[0,5-9]))\d{8}$/g.test(value)) ? '请输入正确的手机号' : undefined
```
##验证IP
```javascript
export const validateIP = (ip) => {
  const reg = /^(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$/;
  return reg.test(ip) ? undefined : '请输入有效的IP地址'
}
```
##验证是否包含中文
```javascript
var cnPattern = /^[\u4E00-\u9FA5]*$/; // 匹配仅允许输入中文
export const isHaveCh = value => (value && !/^[^\u4E00-\u9FA5]*$/.test(value)
  ? true
  : '值不包含中文文本')
```
##验证是否是url
```javascript
export const isURL = (s) => {
  return /^http[s]?:\/\/.*/.test(s);
};
```
## 验证数据类型
```javascript
// 是否是字符串
export const isString = (o) => {
  return Object.prototype.toString.call(o).slice(8, -1) === "String";
};
// 是否是数字
export const isNumber = (o) => {
  return Object.prototype.toString.call(o).slice(8, -1) === "Number";
};
// 是否是bool
export const isBoolean = (o) => {
  return Object.prototype.toString.call(o).slice(8, -1) === "Boolean";
};
// 是否是函数
export const isFunction = (o) => {
  return Object.prototype.toString.call(o).slice(8, -1) === "Function";
};
// 是否是null
export const isNull = (o) => {
  return Object.prototype.toString.call(o).slice(8, -1) === "Null";
};
// 是否是undefined
export const isUndefined = (o) => {
  return Object.prototype.toString.call(o).slice(8, -1) === "Undefined";
};
// 是否是对象
export const isObj = (o) => {
  return Object.prototype.toString.call(o).slice(8, -1) === "Object";
};
// 是否是数组
export const isArray = (o) => {
  return Object.prototype.toString.call(o).slice(8, -1) === "Array";
};
// 是否是日期
export const isDate = (o) => {
  return Object.prototype.toString.call(o).slice(8, -1) === "Date";
};
// 是否是正则
export const isRegExp = (o) => {
  return Object.prototype.toString.call(o).slice(8, -1) === "RegExp";
};
// 是否是错误
export const isError = (o) => {
  return Object.prototype.toString.call(o).slice(8, -1) === "Error";
};
// 是否是Symbol
export const isSymbol = (o) => {
  return Object.prototype.toString.call(o).slice(8, -1) === "Symbol";
};
// 是否是Promise
export const isPromise = (o) => {
  return Object.prototype.toString.call(o).slice(8, -1) === "Promise";
};
// 是否是Set
export const isSet = (o) => {
  return Object.prototype.toString.call(o).slice(8, -1) === "Set";
};
// 移除html标签
export const removeHtmltag = (str) => {
  return str.replace(/<[^>]+>/g, "");
};

// 去除空格
// t: 1-所有空格 2-前后空格 3-前空格 4-后空格
export const trim = (str, t) => {
  let type = t || 1;
  switch (type) {
    case 1:
      return str.replace(/\s+/g, "");
    case 2:
      return str.replace(/(^\s*)|(\s*$)/g, "");
    case 3:
      return str.replace(/(^\s*)/g, "");
    case 4:
      return str.replace(/(\s*$)/g, "");
    default:
      return str;
  }
};
```