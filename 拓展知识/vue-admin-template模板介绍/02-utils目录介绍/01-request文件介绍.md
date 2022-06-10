# request文件

## 文件内容

```js
import axios from 'axios'
import { MessageBox, Message } from 'element-ui'
import store from '@/store'
import { getToken } from '@/utils/auth'

// 创建axios实例对象
const service = axios.create({
  // api的base url, url = base url + request url
  // VUE_APP_BASE_API定义在.env.development文件中
  baseURL: process.env.VUE_APP_BASE_API,
  // 请求超时时间, 超过5000ms没有响应则报错
  timeout: 5000
})

// request拦截器
service.interceptors.request.use(
  config => {
    if (store.getters.token) {
      config.headers['X-Token'] = getToken()
    }
    return config
  },
  error => {
    console.log(error)
    return Promise.reject(error)
  }
)

// response拦截器
service.interceptors.response.use(
  // 处理response情况
  response => {
    const res = response.data
    if (res.code !== 20000) {
		//输出错误信息的一堆代码
    } else {
      // 状态码是20000, 正常返回数据
      return res
    }
  },

  // 处理error情况
  error => {
    console.log('err' + error) // for debug
    Message({
      message: error.message,
      type: 'error',
      duration: 5 * 1000
    })
    return Promise.reject(error)
  }
)

export default service
```

## 文件功能

整个request文件相当于一个封装了axios请求的函数, 需要传入参数`requestUrl`