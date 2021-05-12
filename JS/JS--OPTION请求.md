## OPTION
### 简单请求与复杂请求
```javascript
// 简单请求
以下三项必须都成立才是简单请求
1. 只能是GET、HEAD、POST方法
2. 除了浏览器自己在Http头上加的信息（如Connection、User-Agent），开发者只能加这几个：Accept、Accept-Language、Content-Type。
3. Content-Type只能取这几个值：application/x-www-form-urlencoded
multipart/form-data
text/plain

符合以下任一情况的就是复杂请求：
1.使用方法put或者delete;
2.发送json格式的数据（content-type: application/json）
3.请求中带有自定义头部；
除了满足以上条件的复杂请求其他的就是简单请求。
```