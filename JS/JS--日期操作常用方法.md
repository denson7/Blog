
## Date()常用操作
### 获取时间戳
```
// 获取当前时间的时间戳,精确到毫秒
let _timestamp = new Date().getTime(); 
// 获取指定日期的时间戳,精确到毫秒
let _timestamp = new Date('2018/11/13 20:59:10').valueOf();
console.log(_timestamp); //1542113732993

// 日期格式的字符串转换为时间戳
var str = "2018-11-13 20:59:10";
var _timestamp = new Date(str.replace(/-/g, '/')).getTime(); //2018/11/13 20:59:10
console.log(_timestamp); //1542113950000
```

### 时间戳转为日期
```
// var date = new Date(时间戳); // 获取一个时间对象
// 拼接字符串
var date = new Date(1542113950000);
date.getFullYear(); // 获取完整的年份(4位,1970)
date.getMonth(); // 获取月份(0-11,0代表1月,用的时候记得加上1)
date.getDate(); // 获取日(1-31)
date.getTime(); // 获取时间(从1970.1.1开始的毫秒数)
date.getHours(); // 获取小时数(0-23)
date.getMinutes(); // 获取分钟数(0-59)
date.getSeconds(); // 获取秒数(0-59)
// 例如
const Y = date.getFullYear() + '-';
const M = (date.getMonth() + 1 < 10 ? '0' + (date.getMonth() + 1) : date.getMonth() + 1) + '-';
const D = date.getDate() + ' ';
const h = date.getHours() + ':';
const m = date.getMinutes() + ':';
const s = date.getSeconds();
console.log(Y + M + D + h + m + s); // 2018-11-13 20:59:10
```

### 获取日期字符串
```
# 获取当前日期，形如YYYY-MM-DD HH:mm:ss
var str = new Date().toLocaleString('cn',{hour12:false}); // 2018/11/13 21:09:08
var str1 = str.replace(/\//g,'-'); //2018-11-13 21:09:08
# 获取前一天的日期
var str = new Date(new Date().setDate(new Date().getDate()-1)).toLocaleString('cn',{hour12:false}); //2018/11/12 21:12:29
```
### 差值
```
const DiffTime= (faultDate, completeTime) => {
  if (faultDate == undefined || completeTime == undefined) {
    return '';
  }
  var stime = Date.parse(new Date(faultDate));
  var etime = Date.parse(new Date(completeTime));
  var usedTime = etime - stime; // 两个时间戳相差的毫秒数
  var days = Math.floor(usedTime / (24 * 3600 * 1000));
  var leave1 = usedTime % (24 * 3600 * 1000); // 计算天数后剩余的毫秒数
  var hours = Math.floor(leave1 / (3600 * 1000));
  var leave2 = leave1 % (3600 * 1000); // 计算小时数后剩余的毫秒数
  var minutes = Math.floor(leave2 / (60 * 1000));
  const second = leave2 / 1000 % 60;
  var time = (days > 0 ? days + 'd' : '') +
    (hours > 0 || days > 0 ? hours + 'h' : '') +
    (hours > 0 || days > 0 || minutes > 0 ? minutes + 'm' : '') +
    (hours > 0 || days > 0 || minutes > 0 || second > 0 ? second + 's' : '');
  if (time == '') {
    time = '0s';
  }
  return time;
};
// 使用
DiffTime('2018-12-24 14:20:11','2018/12/26 14:20:12')
"2d0h0m1s"
```
### 格式化
#### 自定义函数
```
#在Date对象的原型链上添加一个format方法
Date.prototype.format = function(format) {
  var date = {
    "M+": this.getMonth() + 1,
    "d+": this.getDate(),
    "h+": this.getHours(),
    "m+": this.getMinutes(),
    "s+": this.getSeconds(),
    "q+": Math.floor((this.getMonth() + 3) / 3),
    "S+": this.getMilliseconds()
  };
  if (/(y+)/i.test(format)) {
    format = format.replace(RegExp.$1, (this.getFullYear() + '').substr(4 - RegExp.$1.length));
  }
  for (var k in date) {
    if (new RegExp("(" + k + ")").test(format)) {
      format = format.replace(RegExp.$1, RegExp.$1.length == 1
        ? date[k] : ("00" + date[k]).substr(("" + date[k]).length));
    }
  }
  return format;
}
//使用
var date = new Date();
date.format("YYYY:MM:dd");
"2018:11:13"
date.format("YYYY:MM:dd hh:mm:ss");
"2018:11:13 21:34:13"

#定义两个参数
const dateFormat = (date, fmt) => {
  var o = {
    'M+': date.getMonth() + 1, // 月份
    'd+': date.getDate(), // 日
    'h+': date.getHours(), // 小时
    'm+': date.getMinutes(), // 分
    's+': date.getSeconds(), // 秒
    'q+': Math.floor((date.getMonth() + 3) / 3), // 季度
    S: date.getMilliseconds() // 毫秒
  };
  if (/(y+)/.test(fmt)) {
    fmt = fmt.replace(RegExp.$1, (date.getFullYear() + '').substr(4 - RegExp.$1.length));
  }
  for (var k in o) {
    if (new RegExp('(' + k + ')').test(fmt)) {
      fmt = fmt.replace(RegExp.$1, (RegExp.$1.length == 1) ? (o[k]) : (('00' + o[k]).substr(('' + o[k]).length)));
    }
  }
  return fmt;
};
//使用
dateFormat(new Date(),"MM dd yyyy hh-mm-ss")
"12 24 2018 11-59-05"
```
#### 使用moment
```
import moment from 'moment';
//示例
moment(new Date()).format("YYYY-MM-DD hh:mm:ss");
"2018-11-13 09:45:51"
moment("20111031", "YYYYMMDD").fromNow();
"7 years ago"
//返回对象
moment().toObject();
{years: 2018, months: 10, date: 13, hours: 21, minutes: 49, …}
//返回数组
moment().toArray();
(7) [2018, 10, 13, 21, 49, 40, 840]
//获取月份的天数
moment().daysInMonth();
30
moment("2019-02", "YYYY-MM").daysInMonth()
28
//返回一个Unix时间戳
moment().unix();
1542117036
```