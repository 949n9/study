## axios 取消请求以及阻止重复请求

在实际项目中，我们可能需要对请求进行“防抖”处理。这里主要是为了阻止用户在某些情况下短时间内重复点击某个按钮，导致前端向后端重复发送多次请求。这里我列举两种比较常见的实际情况：

* PC端 - 用户双击搜索按钮，可能会触发两次搜索请求
* 移动端 - 因移动端没有点击延迟，所以极易造成误操作或多操作，造成请求重发

以上情况有可能在有Loading遮罩时依然发生，所以我们要考虑前端阻止重复请求的方法。
    

**核心`CancelToken`**

在Axios中取消请求的核心方法是`CancelToken`，官网文档中有两种方法。

```js
//第一种

const CancelToken = axios.CancelToken;
const source = CancelToken.source();

axios.get('/user/12345', {
  // 必须对请求进行cancelToken设置
  cancelToken: source.token
}).catch(function (thrown) {
  // 如果请求被取消则进入该方法判断
  if (axios.isCancel(thrown)) {
    console.log('Request canceled', thrown.message);
  } else {
    // handle error
  }
});

// 取消上面的请求
// source.cancel('messge') message为可选项，必须为String
source.cancel('Operation canceled by the user.');
```

```js
//第二种

const CancelToken = axios.CancelToken;
let cancel;

axios.get('/user/12345', {
  // 在options中直接创建一个cancelToken对象
  cancelToken: new CancelToken(function executor(c) {
    cancel = c;
  })
});

// 取消上面的请求
cancel();
```

上文已对axios中的核心方法进行了举例，但是在实际中我们往往不会像官网例子中那样使用，更多的是在axios的拦截器中做全局配置管理。这样的话我们需要对上面的代码进行一些改变。

这里说一下我实现的大体思路：

1. 我们需要对所有正在进行中的请求进行缓存。在请求发起前判断缓存列表中该请求是否正在进行，如果有则取消本次请求。
2. 在任意请求完成后，需要在缓存列表中删除该次请求，以便可以重新发送该请求。

```js
const CancelToken = axios.CancelToken
const source = CancelToken.source()
// 正在进行中的请求列表
let reqList = []

/**
 * 阻止重复请求
 * @param {array} reqList - 请求缓存列表
 * @param {string} url - 当前请求地址
 * @param {function} cancel - 请求中断函数
 * @param {string} errorMessage - 请求中断时需要显示的错误信息
 */
const stopRepeatRequest = function (reqList, url, cancel, errorMessage) {
  const errorMsg = errorMessage || ''
  for (let i = 0; i < reqList.length; i++) {
    if (reqList[i] === url) {
      cancel(errorMsg)
      return
    }
  }
  reqList.push(url)
}

/**
 * 允许某个请求可以继续进行
 * @param {array} reqList 全部请求列表
 * @param {string} url 请求地址
 */
const allowRequest = function (reqList, url) {
  for (let i = 0; i < reqList.length; i++) {
    if (reqList[i] === url) {
      reqList.splice(i, 1)
      break
    }
  }
}

const service = axios.create()

// 请求拦截器
service.interceptors.request.use(
  config => {
  	// 设置cancelToken对象
    config.cancelToken = source.token
    // 阻止重复请求。当上个请求未完成时，相同的请求不会进行
    stopRepeatRequest(reqList, config.url, source.cancel, `${config.url} 请求被中断`)
    return config
  },
  err => Promise.reject(err)
)

// 响应拦截器
service.interceptors.response.use(
  response => {
    // 增加延迟，相同请求不得在短时间内重复发送
    setTimeout(() => {
      AjaxApi.allowRequest(reqList, response.config.url)
    }, 1000)
    // ...请求成功后的后续操作
    // successHandler(response)
  },
  error => {
    if (axios.isCancel(thrown)) {
      console.log(thrown.message);
    } else {
      // 增加延迟，相同请求不得在短时间内重复发送
      setTimeout(() => {
        AjaxApi.allowRequest(reqList, error.config.url)
      }, 1000)
    }
    // ...请求失败后的后续操作
    // errorHandler(error)
  }
)

```



## 拦截器

在请求或响应被 `then` 或 `catch` 处理前拦截它们。

```js
// 添加请求拦截器
axios.interceptors.request.use(function (config) {
    // 在发送请求之前做些什么
    return config;
  }, function (error) {
    // 对请求错误做些什么
    return Promise.reject(error);
  });

// 添加响应拦截器
axios.interceptors.response.use(function (response) {
    // 对响应数据做点什么
    return response;
  }, function (error) {
    // 对响应错误做点什么
    return Promise.reject(error);
  });
```







### axios的封装

1.跨域请求携带cookie

```
axios.defaults.withCredentials = true
```

> 疑问： 跨域携带cookie的安全性问题，会导致CSRF 攻击吗？

2.引入了qs模块

`qs`模块是用来序列化js数据的。

```js
{"uid":"cs11","pwd":"000000als","username":"cs11","password":"000000als"}//JSON.stringify    uid=cs11&pwd=000000als&username=cs11&password=000000als //qs.stringify
```
基本用法：
```js
let qs = require('qs')
temp = qs.stringify(params)
```



比较标准的封装流程：

```JS
/**axios封装
 * 请求拦截、相应拦截、错误统一处理
 */
import axios from 'axios';import QS from 'qs';
import { Toast } from 'vant';
import store from '../store/index'

// 环境的切换
if (process.env.NODE_ENV == 'development') {    
    axios.defaults.baseURL = '/api';
} else if (process.env.NODE_ENV == 'debug') {    
    axios.defaults.baseURL = '';
} else if (process.env.NODE_ENV == 'production') {    
    axios.defaults.baseURL = 'http://api.123dailu.com/';
}

// 请求超时时间
axios.defaults.timeout = 10000;

// post请求头
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded;charset=UTF-8';

// 请求拦截器
axios.interceptors.request.use(    
    config => {
        // 每次发送请求之前判断是否存在token，如果存在，则统一在http请求的header都加上token，不用每次请求都手动添加了
        // 即使本地存在token，也有可能token是过期的，所以在响应拦截器中要对返回状态进行判断
        const token = store.state.token;        
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
                // 401: 未登录                
                // 未登录则跳转登录页面，并携带当前页面的路径                
                // 在登录成功后返回当前页面，这一步需要在登录页操作。                
                case 401:                    
                    router.replace({                        
                        path: '/login',                        
                        query: { redirect: router.currentRoute.fullPath } 
                    });
                    break;
                // 403 token过期                
                // 登录过期对用户进行提示                
                // 清除本地token和清空vuex中token对象                
                // 跳转登录页面                
                case 403:                     
                    Toast({                        
                        message: '登录过期，请重新登录',                        
                        duration: 1000,                        
                        forbidClick: true                    
                    });                    
                    // 清除token                    
                    localStorage.removeItem('token');                    
                    store.commit('loginSuccess', null);                    
                    // 跳转登录页面，并将要浏览的页面fullPath传过去，登录成功后跳转需要访问的页面
                    setTimeout(() => {                        
                        router.replace({                            
                            path: '/login',                            
                            query: { 
                                redirect: router.currentRoute.fullPath 
                            }                        
                        });                    
                    }, 1000);                    
                    break; 
                // 404请求不存在                
                case 404:                    
                    Toast({                        
                        message: '网络请求不存在',                        
                        duration: 1500,                        
                        forbidClick: true                    
                    });                    
                break;                
                // 其他错误，直接抛出错误提示                
                default:                    
                    Toast({                        
                        message: error.response.data.message,                        
                        duration: 1500,                        
                        forbidClick: true                    
                    });            
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
export const get =(url, ...params)=>{    
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
export const post = (url,... params) =>{    
    return new Promise((resolve, reject) => {         
        axios.post(url, QS.stringify(...params))        
        .then(res => {            
            resolve(res.data);        
        })        
        .catch(err => {            
            reject(err.data)        
        })    
    });
}



```





https://www.jianshu.com/p/78dac627d9ea