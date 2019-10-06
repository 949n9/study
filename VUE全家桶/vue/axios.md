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

