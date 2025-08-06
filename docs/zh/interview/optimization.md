## 1.webpack 常见优化手段

::: warning 提升开发体验
&emsp;&emsp;使用 **source-map** 让开发或上线时代码报错时能有更加准确的错误提示
:::
::: warning 提升打包构建速度
&emsp;&emsp;使用 **HotModuleReplacement(HMR)** 让开发时只需重新编译有变化的代码，不变的代码使用缓存，从而使更新速度更快  
&emsp;&emsp;使用 **OneOf** 让资源文件只会被一种规则匹配，不会继续遍历，提升打包速度  
&emsp;&emsp;使用 **include/exclude** 只检测或排除某些文件，处理文件更少，提升打包速度  
&emsp;&emsp;使用 **cache** 对 eslint 和 babel 处理的结果进行缓存，二次打包更快  
&emsp;&emsp;使用 **Thread** 开启多进程打包，提升打包速度（_注意：进程开启需要 600ms 左右的时间，因此只对多文件处理有效，若文件较少，则适得其反_）
:::
::: warning 减少代码体积
&emsp;&emsp;使用 **Tree Shaking** 剔除没有使用的代码  
&emsp;&emsp;@babel/plugin-transform-runtime 插件对 babel 进行处理，让辅助代码从中引入，而不是每个文件都生成辅助代码  
&emsp;&emsp;Image minimizer 对本地图片进行压缩
:::
::: warning 优化代码运行性能
&emsp;&emsp;使用 **code split** 分割代码为多个 js 文件，从而是单个文件体积更小，并且加载更快，同时使用 **import 动态导入**语法进行按需加载  
&emsp;&emsp;使用 **preload/prefetch** 对代码进行提前加载  
&emsp;&emsp;使用 **network cache** 把模块之间的依赖关系保存在一个 runtime 文件中，若依赖的模块发生修改，则会修改 runtime 文件，而不是修改主模块。这样做的好处是：主模块的缓存不会失效，打包编译效率更高  
&emsp;&emsp;使用 **core-js** 进行兼容性处理  
&emsp;&emsp;使用 **PWA** 让代码离线也能访问
:::

## 2.前端首页白屏优化

::: warning
① 路由懒加载、图片懒加载  
对于 SPA 项目，当打开首页时，会一次性加载所有的资源，其中包括首页没有使用到的资源，造成首页加载慢  
② 组件懒加载  
适用条件=>  
&emsp;1)于体积大的文件，利用组件进行资源拆分  
&emsp;2)需要特定条件才能触发的组件，入弹框  
&emsp;3)复用性高的组件，可以有效利用缓存  
③ 合理利用 TreeShkaing  
④ 使用骨架屏  
项目打包时将骨架屏内容放到 html 根节点中，减少操作 dom 的次数  
⑤ 虚拟列表  
减少了渲染条数  
⑥Web Worker 优化长任务  
⑦js 延迟加载
:::
