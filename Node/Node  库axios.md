[TOC]
## Axios使用
```
axios介绍：
1.基于promise的异步ajax请求库
2.浏览器端/node端都可以使用
3.支持请求/响应拦截器
4.支持请求取消
5.请求/响应数据转换
6.批量发送多个请求
```
```
https://github.com/axios/axios
```
### 设置不同的请求方式
```javascript
// 1. Content-Type: application/json; charset:utf-8; 设置header头信息
import axios from 'axios';

const data1 = {code: '1234', name: 'yyyy'}; // 第二个参数是对象
axios.post(`${this.$url}/test/testRequest`, data1).then(res => {
  console.log('res=>', res);
});

// 2. Content-Type: multipart/form-data, 注意提交的数据必须是formData数据
const data2 = new FormData();
data2.append('code', '1234');
data2.append('name', 'yyyy'); // 第二个参数是formData格式
axios.post(`${this.$url}/test/testRequest`, data2).then(res => {
  console.log('res=>', res);
});

// 3. Content-Type: application/x-www-form-urlencoded
import qs from 'Qs';

const data3 = {code: '1234', name: 'yyyy'}; // 第二个参数是字符串
axios.post(`${this.$url}/test/testRequest`, qs.stringify({data3})).then(res => {
  console.log('res=>', res);
});
```
### axios的post封装
```javascript
import axios from 'axios';
import Qs from 'qs';

const API_SERVER = 'http://xxx.com';

export const post = (url, param, baseServer = API_SERVER) => {
  const data = param.data;
  axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded; charset=UTF-8';
  const config = {
    url,
    withCredentials: true, // 允许携带cookie
    // 请求方法同上
    method: 'post', // default
    // 基础url前缀
    baseURL: baseServer,
    transformRequest: [function (data) {
      data = Qs.stringify(data);
      return data;
    }],
    transformResponse: [function (data) {
      // 这里提前处理返回的数据
      return data;
    }],
    // 请求头信息
    headers: { 'X-Requested-With': 'XMLHttpRequest' },
    // parameter参数
    params: {
      timestamp: Date.parse(new Date()) / 1000
    },
    // post参数，使用axios.post(url,{},config);如果没有额外的也必须要用一个空对象，否则会报错
    data,
    // 设置超时时间
    timeout: 60000,
    // 返回数据类型
    responseType: 'json' // default
  };
  // return axios.post(url, data, config).then(resp => ({
  //   json: resp.data
  // }));

  return axios.post(url, data, config).then((resp) => {
    // 适配错误提示
    if(resp.data.retcode != 0) {
      resp.data.message = resp.data.errmsg ? resp.data.errmsg : '系统繁忙，请稍后再试！';
    }
    return {
      json: resp.data
    };
  });
};
```
```javascript
/**
 * axios封装
 * 请求拦截、相应拦截、错误统一处理
 */
import axios from 'axios';
import QS from 'qs';


// 环境的切换
if (process.env.NODE_ENV === 'development') {
  axios.defaults.baseURL = '/api';
} else if (process.env.NODE_ENV === 'debug') {
  axios.defaults.baseURL = '';
} else if (process.env.NODE_ENV === 'production') {
  axios.defaults.baseURL = 'http://api.123dailu.com/';
}

// 请求超时时间
axios.defaults.timeout = 10000;

// post请求头
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded;charset=UTF-8';

// 请求拦截器
axios.interceptors.request.use(
  config => {
    // 设置token
    const token = 'xxxxxx';
    token && (config.headers.Authorization = token);
    return config;
  },
  error => {
    return Promise.error(error);
  })

// 响应拦截器
axios.interceptors.response.use(
  response => {
    if (response.status === 200) {
      return Promise.resolve(response);
    } else {
      return Promise.reject(response);
    }
  },
  // 服务器状态码不是200的情况
  error => {
    if (error.response.status) {
      switch (error.response.status) {
        case 50001:
          // todo
          break;
        default:
         // todo
      }
      return Promise.reject(error.response);
    }
  }
);

/**
 * get方法，对应get请求
 * @param {String} url [请求的url地址]
 * @param {Object} params [请求时携带的参数]
 */
export function get(url, params){
  return new Promise((resolve, reject) =>{
    axios.get(url, {
      params: params
    })
      .then(res => {
        resolve(res.data);
      })
      .catch(err => {
        reject(err.data)
      })
  });
}

/**
 * post方法，对应post请求
 * @param {String} url [请求的url地址]
 * @param {Object} params [请求时携带的参数]
 */
export function post(url, params) {
  return new Promise((resolve, reject) => {
    axios.post(url, QS.stringify(params))
      .then(res => {
        resolve(res.data);
      })
      .catch(err => {
        reject(err.data)
      })
  });
}
```
### axios并发请求
```javascript
function getData1() {
  return axios.get('/user');
}

function getData2() {
  return axios.get('/list/');
}
// 并发请求
axios.all([getData1(), getData2()])
  .then(axios.spread(function (data1, data2) {
    console.log('两个请求都完成了', data1, data2)
  }))
  .catch(function (error) {
    console.log(error);
  });
```
### 取消请求
基本用法
```javascript
// 使用 CancelToken。source 工厂方法创建 cancel token
const source = axios.CancelToken.source();

//  get请求
axios
    .get('/user/12345', {
        cancelToken: source.token,
    })
    .catch(function(thrown) {
        if (axios.isCancel(thrown)) {
            console.log('Request canceled', thrown.message);
        } else {
            // 处理错误
        }
    });
// post请求
axios.post('/user/12345', { name: 'new name' }, {
        cancelToken: source.token,
    }
);

// 需要取消请求时，调用 message 可选
source.cancel('Abort Request');
```
### 防止重复提交
```javascript
<!DOCTYPE html>
<html lang="zh-cn">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Axios 取消重复请求示例</title>
    <script src="https://cdn.bootcdn.net/ajax/libs/qs/6.9.6/qs.min.js"></script>
    <script src="https://cdn.bootcdn.net/ajax/libs/axios/0.21.1/axios.min.js"></script>
  </head>
  <body>
    <h3>Axios 取消重复请求示例</h3>
    <button onclick="sendRequest()">发起请求</button>
  </body>
  <script>
    const pendingRequest = new Map();

    function generateReqKey(config) {
      const { method, url, params, data } = config;
      return [method, url, Qs.stringify(params), Qs.stringify(data)].join('&');
    }

    function addPendingRequest(config) {
      const requestKey = generateReqKey(config);
      config.cancelToken =
        config.cancelToken ||
        new axios.CancelToken((cancel) => {
          if (!pendingRequest.has(requestKey)) {
            pendingRequest.set(requestKey, cancel);
          }
        });
    }

    function removePendingRequest(config) {
      const requestKey = generateReqKey(config);
      if (pendingRequest.has(requestKey)) {
        const cancel = pendingRequest.get(requestKey);
        cancel(requestKey);
        pendingRequest.delete(requestKey);
      }
    }

    axios.interceptors.request.use(
      function (config) {
        removePendingRequest(config); // 检查是否存在重复请求，若存在则取消已发的请求
        addPendingRequest(config); // 把当前请求添加到pendingRequest对象中
        return config;
      },
      (error) => {
        return Promise.reject(error);
      }
    );

    axios.interceptors.response.use(
      (response) => {
        removePendingRequest(response.config); // 从pendingRequest对象中移除请求
        return response;
      },
      (error) => {
        removePendingRequest(error.config || {}); // 从pendingRequest对象中移除请求
        if (axios.isCancel(error)) {
          console.log('已取消的重复请求：' + error.message);
        } else {
          // 添加异常处理
        }
        return Promise.reject(error);
      }
    );

    async function sendRequest() {
      console.dir(pendingRequest);
      const response = await axios.get(
          "https://localhost:3000/test"
      );
      console.log(response.data);
    }
  </script>
</html>
```


### 重试
```
https://www.npmjs.com/package/axios-retry
```
#### 方法1 使用适配器
```javascript
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Axios 请求重试示例（适配器）</title>
  </head>
  <body>
    <button onclick="requestWithoutRetry()">请求失败不重试</button>
    <button onclick="requestWithRetry()">请求失败重试</button>
  </body>
  <script>
    function retryAdapterEnhancer(adapter, options) {
      const { times = 0, delay = 300 } = options;
      return async (config) => {
        const { retryTimes = times, retryDelay = delay } = config;
        let __retryCount = 0;
        const request = async () => {
          try {
            return await adapter(config);
          } catch (err) {
            if (!retryTimes || __retryCount >= retryTimes) {
              return Promise.reject(err);
            }
            __retryCount++;
            // 延时处理
            const delay = new Promise((resolve) => {
              setTimeout(() => {
                resolve();
              }, retryDelay);
            });
            // 重新发起请求
            return delay.then(() => {
              return request();
            });
          }
        };
        return request();
      };
    }

    const http = axios.create({
      baseURL: 'http://localhost:3000/',
      adapter: retryAdapterEnhancer(axios.defaults.adapter, {
        retryDelay: 1000,
      }),
    });

    function requestWithoutRetry() {
      http.get('/users');
    }

    function requestWithRetry() {
      http.get('/users', { retryTimes: 2 });
    }
  </script>
</html>
```
#### 方法2 使用拦截器
```javascript
<!DOCTYPE html>
<html lang="zh-cn">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Axios 请求重试示例（拦截器）</title>
    <script src="https://cdn.bootcdn.net/ajax/libs/axios/0.21.1/axios.min.js"></script>
  </head>
  <body>
    <h3>Axios 请求重试示例（拦截器）</h3>
    <button onclick="requestWithoutRetry()">请求失败不重试</button>
    <button onclick="requestWithRetry()">请求失败重试</button>
    <script>
      axios.interceptors.response.use(null, (err) => {
        let config = err.config;
        if (!config || !config.retryTimes) return Promise.reject(err);
        const { __retryCount = 0, retryDelay = 300, retryTimes } = config;
        // 在请求对象上设置重试次数
        config.__retryCount = __retryCount;
        // 判断是否超过了重试次数
        if (__retryCount >= retryTimes) {
          return Promise.reject(err);
        }
        // 增加重试次数
        config.__retryCount++;
        // 延时处理
        const delay = new Promise((resolve) => {
          setTimeout(() => {
            resolve();
          }, retryDelay);
        });
        // 重新发起请求
        return delay.then(function () {
          return axios(config);
        });
      });

      function requestWithoutRetry() {
        axios.get('http://localhost:3000/users');
      }

      function requestWithRetry() {
        axios.get('http://localhost:3000/users', { retryTimes: 1 });
      }
    </script>
  </body>
</html>
```