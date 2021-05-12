## RegExp
### 创建一个正则表达式对象
```
#方法一，通过构造函数
var re = new RegExp(/ab+c/, 'i');
//或者
var re = RegExp(pattern [, flags]);
//备注：pattern 表示正则表达式文本，flags可以为g(全局匹配)，i(忽略大小写)等

#方法二，通过字面量，等价于方法一
var re = /ab+c/i;
var re = /pattern/flags;
```
```
//参考文章
https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp
```


## 基础
### 
### 捕获组与非捕获组
`捕获组：`简单来说就是把正则表达式中用括号匹配的内容，保存到内存中，方便调用。`()`小括号括起来的内容就是一个分组。
```javascript
// 使用捕获组的内容
var reg = /(\d{4})-(\d{2}-(\d\d))/;
"2019-12-12".match(reg)
输出：["2019-12-12", "2019", "12-12", "12", index: 0, input: "2019-12-12", groups: undefined]
```
`非捕获组：`非捕获组只匹配结果，不能在表达式和程序中做进一步处理。
```
// 示例,匹配本地IP，?: 就表示括号内的内容不暴露给外部。
/^127(?:\.(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)){3}$/
```
### 反向引用
捕获组捕获到的内容，不仅可以在正则表达式外部通过程序进行引用，也可以在正则表达式内部进行引用，这种引用方式就是反向引用。
```javascript
普通捕获组反向引用：\k<number>，通常简写为\number

命名捕获组反向引用：\k<name>或者\k'name'

```
## 可视化
```javascript
https://jex.im/regulex/
https://regexr.com/
https://www.regextester.com/
```


## 常用方法

### exec()方法
与test()方法类似，只是返回的是匹配的结果。
语法：
```javascript
regexObj.exec(str)
//参数：str表示要匹配的字符串。
//返回值：
//如果匹配成功，返回一个数组。返回的数组将完全匹配成功的文本作为第一项，将正则括号里匹配成功的作为数组填充到后面。
//如果匹配失败，返回null。
// # 示例：获取年-月-日
/(\d{4})-(\d{2})-(\d{2})/.exec("2018-05-16")
// [ '2018-05-16', '2018', '05', '16', index: 0, input: '2018-05-16' ]
/abc(d)/.exec('abcd')  //["abcd","d",index:0,input:"abc"]
/abc(d)/.exec('abc')  //null
```
### test()方法
返回布尔值。
语法：
```javascript
regexObj.test(str)
//参数：str表示要匹配的字符串。
//返回值：
//如果正则表达式与指定的字符串匹配 ，返回true，否则返回false。
// #示例
/abc/.test('abc')  //返回true
/abd/.test('abc')  //返回false
```
### search()方法
返回匹配成功为止的索引，匹配失败，返回-1
```javascript
'abc'.search(/abc/) //0
'abc'.search(/c/) //2
```
### match()方法
与exec()方法类似，返回匹配的结果
```javascript
'abc'.match(/abc/) //['abc',idex:0,input:abc]
'abc'.match(/abd/) //null
```
### replace()方法
将匹配的字符，替换为另外的字符。
```javascript
// 替换手机号中间四位为*号
"18612344567".replace(/(\d{3})\d*(\d{4})/,"$1****$2") // "186****4567"

'abc'.replace('a','b') //bbc
'abc'.replace(/[abc]/,'y') //ybc
'abc'.replace(/[abc]/g,'y') //yyy 全局替换

// 在第二个参数中，也可以引用前面匹配的结果
'abc'.replace(/a/, '$&b') // abbc $& 引用前面的匹配字符
'abc'.replace(/(a)b/, '$1a') // aac &n 引用前面匹配字符的分组
'abc'.replace(/b/, '$\'') // aac $` 引用匹配字符前面的字符
'abc'.replace(/b/, "$'") // acc $' 引用匹配字符后面的字符

// replace的第二个参数也可以是函数，其第一个参数是匹配内容，后面的参数是匹配的分组
'abc'.replace(/\w/g, function (match, $1, $2) {
  return match + '-'
})
// a-b-c-
```
### split()方法
可以用指定符号分隔字符串，并返回数据.
```javascript
'a,b,c'.split(',') // [a, b, c]
// 其参数也可以使一个正则，如果分隔符有多个时，就必须使用正则
'a,b.c'.split(/,|\./) // [a, b, c]
```
### 正向查找
```javascript
// 使用正则表达式 x(?=y) ，匹配后面是y的x**，这里的x和y都代表正则表达式。
// 例如：要匹配出域名segmentfault的话
/[a-z]+(?=\.com)/.exec("https://segmentfault.com/u/denson")[0]
//"segmentfault"
```
### 正向否定查询
```javascript
// 使用正则表达式 x(?!y) ，这里的x和y都代表正则表达式。
// 例如：匹配小数点后面的数字。
/\d+(?!\.)/.exec("3.1415926")[0]
//3.1415926
```
### 示例
```javascript
// 获取正则匹配的内容
function getStr(str) {
  var pattern = />(.+)</;
  //方法一
  var items = str.match(pattern);
  //方法二
  var items = pattern.exec(str);
  return items;
}

// 验证身份证
function checkCardNo(card) {
  var pattern = /(^\d{15}$)|(^\d{18}$)|(^\d{17}(\d|X|x)$)/;
  return pattern.test(card);
}

// 验证电话
function checkNum(phone) {
  var pattern = /^1[34578]\d{9}$/;
  return pattern.test(phone); //正确返回true,错误返回false
}

// 验证密码：只能输入6-20个字母、数字、下划线
function checkPasswd(pass) {
  var pattern = /^(\w){6,20}$/;
  if (!pattern.exec(pass)) return false
  return true
}

// 验证用户名输入是否正确，只能输入4-20个以字母开头、可带数字、“_”、“.”的字串
function checkName(name) {
  var pattern = /^[a-zA-Z]{1}([a-zA-Z0-9]|[._]){3,19}$/;
  if (pattern.exec(name)) { //正确
    return true;
  } else { //错误
    return false;
  }
}

// 验证字符串中是否包含中文
function isContainsZh(str) {
  // Unicode编码中的汉字范围  /[^\x00-\x80]/
  var pattern = /[^\x00-\x80]/;
  if (pattern.exec(str)) { //包含
    return true;
  } else { //不包含
    return false;
  }
}

// 验证不能包含字母
export const isNoWord = value => /^[^A-Za-z]*$/g.test(value);

// 验证版本号格式必须为X.Y.Z
export const isVersion = value => /^\d+(?:\.\d+){2}$/g.test(value);
```
```javascript
// 正则完全匹配形如：key=value key=value 优化
const _validateKeyValueBySpaceReg = (value) => {
  const reg = /^[\w-]+=[\w-]+ ?([\w-]+=[\w-]+ ?)*$/g
  return  value && !reg.test(value)
}

export const ValidateKeyValueBySpaceReg = _validateKeyValueBySpaceReg

export const validateSpace = (value) => value && _validateKeyValueBySpaceReg(value) ? '请输入key=value,参数间用空格分割' : undefined

// 验证IP地址
export const validateIP = (ip) => {
  const reg = /^(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$/;
  return reg.test(ip) ? undefined : '请输入有效的IP地址'
}

// 端口 0-65535
export const ValidatePort = (value) => {
  let flag = false
  // 判断是否是数字
  if (/^[0-9]*$/g.test(value)) {
    // 判断是否超过65535
    if(value < 0 || value > 65535){
      flag = true
    }
  } else {
    flag = true
  }
  return flag ? '端口号仅能输入0-65535之间的数字' : undefined
}

// 表单验证规则
// 数据源名称 仅支持汉字、字母、数字、“_”及“-”的组合
export const ValidateDataName = (value) => !(value && /^[\u4e00-\u9fa5a-zA-Z0-9_-]+$/g.test(value)) ? '仅支持汉字、字母、数字、"_"及"-"的组合,不能超过40字符' : undefined

// 存储桶校验 形如 xxx-1258469122
export const ValidateCvmBucket = (value) => !(value && /^[a-z0-9-]+-\d+$/g.test(value)) ? '仅支持小写字母、数字和"-"的组合，不能超过50字符' : undefined

// 校验文件目录,路径只能由 字母,数字,"_","-",".","/" 组成
export const ValidateFilePath = (value) => value && (/[^a-zA-Z0-9-_\.\/]/g.test(value) || /(\/){2,}/g.test(value))? '路径只能由 字母,数字,"_","-",".","/" 组成' : undefined

// 大于等于1的整数
export const GtEqOneInt = (value) => !(value && /^[1-9]\d*$/g.test(value)) ? '请输入大于等于1的整数' : undefined

// 大于等于0的整数
export const GtEqZeroInt = (value) => !(value && /^\d*$/g.test(value)) ? '请输入大于等于0的整数' : undefined

// 大于等于0的数值，包含小数
export const GtEqZero = (value) => !(value && /^\d+(\.\d+)?$/g.test(value)) ? '请输入大于等于0的数值' : undefined

// 0到1之间的小数, (0,1)   缺陷只能匹配6位，0.0000001为false
export const InZeroAndOne = (value) => !(value && /^0(\.\d{1,6})+$/g.test(value)) ? '请输入0到1之间的小数' : undefined

// 大于0小于等于1的数值，包含小数 (0,1]
export const GtZeroLtEqOne = (value) => !(value && /^(0(\.\d{1,6})+|1)$/g.test(value)) ? '请输入大于0小于等于1的数值' : undefined

// 0到1的数值，包含小数 [0,1]
export const BetweenZeroAndOne = (value) => !(value && /^(0(\.\d{1,6})?|1)$/g.test(value)) ? '请输入0到1的数值' : undefined

// 请输入正确的时间，yyyymm、yyyymmdd、yyyymmddhh 3种格式均可
export const validateDateFormat = (value) => !(value && /^(\d{6}|\d{8}|\d{10})$/g.test(value)) ? '请输入正确的时间格式，仅允许yyyymm、yyyymmdd、yyyymmddhh3种格式' : undefined
```