## 示例
```javascript
const redis = require('redis');
const client = redis.createClient();

const customCallBack = (key, value, expire, callback) => {
  client.set(key, value, function (err, result) {
    if (err) {
      callback(err, null);
      return;
    }
    if (!isNaN(expire) && expire > 0) {
      client.expire(key, parseInt(expire));
    }
    callback(null, result);
  });
};

// 使用
customCallBack('key', 'value', 100, function (err, result) {
  if (err) {
    throw err;
  } else {
    console.log(result);
  }
});
```