## 1. 控制最大并发请求数，同时控制并发任务的执行顺序

基于技术栈：Axios

### **_问题一：控制最大并发请求数_**

> **问题背景**
>
> 1. 批量发送网络请求，若全量发送，则会给服务端造成压力
>
> **解决思路**：基于**浏览器异步的任务队列**方案，通过 JS/TS 模拟一个**任务队列**，达到暂时拦截请求的目的
>
> **过程表述**
>
> 首先，浏览器自身就拥有最大并发数的控制，数量是6，但基于现实情况，可能想把这个数量改为3
>
> 具体做法是，在封装axios的模块中，定义3个变量
>
> - 常量maxConcurrent：要设置的最大并发数
> - 变量concurrentRequests：当前的并发请求数
> - 数组requestQueue：请求队列
>
> 在请求拦截器中，判断 当前请求数 是否小于 最大并发数
>
> - 若小于，则代表还未到达最大并发数，此时 当前请求数+1 ，同时放行请求，即return config
> - 若等于，则代表已达到最大并发数，此时需要暂时拦截，使用Promise把请求包装成异步回调，加入到自定义的请求队列中
>
> ```ts
> service.interceptors.request.use(
>   (config: InternalAxiosRequestConfig) => {
>     if (concurrentRequests < maxConcurrent) {
>       concurrentRequests++
> 
>       return config
>     } else {
>       return new Promise((resolve) => {
>         requestQueue.push({ config, resolve })
>       })
>     }
>   }
> )
> ```
>
> 在请求拦截器中，当前请求数-1
>
> 然后判断，自定义的请求队列中是否存在任务，即 requestQueue.length > 0
>
> - 若存在，则通过shift方法取出第一个任务，使用Promise的resolve方法执行
>
> ```ts
> service.interceptors.response.use(
>   async (response: AxiosResponse) => {
>     const { config, data } = response
>     const { code, message } = data
> 
>     concurrentRequests--
>     if (requestQueue.length > 0) {
>       const { config, resolve } = requestQueue.shift()
>       concurrentRequests++
>       resolve(config)
>     }
> 
>     return data
>   }
> )
> ```

### 问题二：控制并发任务的执行顺序

**_问题二：相对路径引入 CDN，即与当前域名一致_**

> **问题背景**
>
> 1. 由于业务需要，生产环境的 CDN 地址与前端代码部署的地址一致，因此要利用浏览器的自动补全 url 机制，实现 cdn 的相对路径引入，且开发环境（本机地址）不受影响
>
> **解决思路**：开发环境不受影响，意味着别名的路径指向不能变更，因此需要修改打包产物
>
> **过程表述**
>
> 首先，手动修改打包产物是一个万能且简单的方案，无论是 VSCODE 还是其他编辑器，都有提供批量替换的功能。但是这种方案存在着一些缺点，比如：操作繁琐，每一次打包都需要手动执行一次替换操作；又比如：不利于 CI/CD 流程
>
> **问题背景**
>
> 1. 进入系统之前，需要请求n个接口（n >= 3)，同时页面也有不定量的接口，那么这n个接口可能会堵塞页面的接口，造成页面渲染时间变长
>
> **解决思路**：基于上述的自定义队列方案，控制加入数组的方式，即**头插和尾插**
>
> **过程表述**
>
> - 拓展axios类型，加入isFirst配置，代表是否需要优先执行，默认值为true
>
>   ```ts
>   declare module 'axios' {
>     export interface AxiosRequestConfig {
>       isFirst?: boolean
>     }
>   }
>   
>   const service = axios.create({
>     baseURL: import.meta.env.VITE_API_BASE_URL,
>     timeout: 10000,
>     isFirst: true,
>     headers: {
>       'Content-Type': 'application/json',
>     },
>   })
>   ```
>
> - 判断isFirst：为true，则头插；为false，则尾插
>
>   ```ts
>   service.interceptors.request.use(
>     (config: InternalAxiosRequestConfig) => {
>       if (concurrentRequests < maxConcurrent) {
>         concurrentRequests++
>   
>         return config
>       } else {
>         return new Promise((resolve) => {
>           if (config.isFirst) {
>             requestQueue.unshift({ config, resolve })
>           } else {
>             requestQueue.push({ config, resolve })
>           }
>         })
>       }
>     }
>   )
>   ```
