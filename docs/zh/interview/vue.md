## 1.Vue 生命周期分别做了什么事情

|     hooks     |                                                   status                                                   |                        实践                        |
| :-----------: | :--------------------------------------------------------------------------------------------------------: | :------------------------------------------------: |
| beforeCreate  |                  实例初始化之后，this 指向创建的实例，此时不能访问到 data、methods 等属性                  |              常用于初始化非响应式变量              |
|    created    |               实例创建完成，此时可访问 data、method 等属性；不能访问到$el，无法获取组件实例                |        常用于简单的 ajax 请求，页面的初始化        |
|  beforeMount  |                     在挂载开始之前被调用，进行模版编译，把 template 编译成 render 函数                     |                         --                         |
|    mounted    |                     实例挂载到 DOM 上，此时可以访问$el、$ref，因此可以获取到 DOM 节点                      |       常用于获取 VNode 信息和操作，ajax 请求       |
| beforeUpdate  |                                 响应式数据更新时调用，获取的是更新前的 DOM                                 |            手动移除已添加的事件监听器量            |
|    updated    |                              组件更新后立刻调用，可执行依赖于最新 DOM 的操作                               |    避免在这个钩子函数中操作数据，可能陷入死循环    |
| beforeDestroy |                      实例销毁之前调用。这一步，实例仍然完全可用，this 仍能获取到实例                       | 常用于销毁定时器、解绑全局事件、销毁插件对象等操作 |
|   destroyed   | 实例销毁后调用，调用后，Vue 实例指示的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁 |                         --                         |

## 2.keep-alive 及其生命周期

::: warning
**keep-alive** 是一个抽象组件：它自身不会渲染一个 DOM 元素，也不会出现在父组件链中
:::
| hooks | status |  
| :---: | :----: |
| activated | 在第一次渲染时调用，以及后续每次激活时调用 |
| deactivated | 组件停用时调用 |

## 3.v-for 为什么要加上 key

::: warning 提升性能
&emsp;① Vue 通过 diff 算法去比较 VDom 的变化，而 **key** 则为每个 VNode 添加了**唯一标识**，使得 diff 算法可以更高效的比较 VDom 的变化  
&emsp;② 如果 v-for 用于渲染组件，不提供 key 的话，每次重新渲染都会创建**新的实例**，并销毁旧的。而新的组件实例不会继承原有的状态，因此会造成组件**内部状态丢失**的问题，如输入框中已输入的内容
:::
::: tip 为什么不建议用 index 作为 key
&emsp;index 有可能会变，起不到**唯一标识**的作用

```js
const arr = [
	{ age: 18, name: 'vladimir' },
	{ age: 19, name: 'screeper' },
	{ age: 20, name: 'coder' },
	{ age: 24, name: 'ghost' }
]
arr.unshift({ age: 23, name: 'codemaker' })
```

在数组的头部插入新元素，则原数组的所有 index 值都发生了改变，因此 key 的效果无法达到
:::

## 4.谈谈 Vue 响应式 和 双向绑定 的原理

当数据发生变化时，对应的视图也随之更新  
:::danger 响应式
响应式一定是：**数据与函数**的绑定，这里的函数一般指的是用到此数据的**渲染函数**  
此外，计算属性 computed 也是此原理，利用数据与回调函数进行绑定，实现了**数据之间的关联**，关联的桥梁正是**回调函数**
:::
:::danger 双向绑定
_Vue2_ 基于 **Object.defineProperty**，并结合**发布订阅**模式  
_Vue3_ 基于 **Proxy**  
已 Vue2 为例，通过遍历 data 对象的所有属性，使其转化为 **getter 和 setter**  
在 getter 里进行**依赖收集**，即记录用到此变量的所有函数  
在 setter 里进行**派发更新**，即运行刚刚记录到的所有渲染函数

```js
const observe = obj => {
	for (let key in obj) {
		let internalValue = obj[key]
		let funcs = new Set()
		Object.defineProperty(obj, key, {
			get: function () {
				// 依赖收集， 记录：哪个函数用到了此变量
				window.__func && funcs.add(window.__func)
				return internalValue
			},
			set: function (val) {
				internalValue = val

				// 派发更新， 运行：执行所有用到此变量的函数
				funcs.forEach(func => {
					func()
				})
			}
		})
	}
}

// 辅助函数，把函数名记录到全局变量上，方便进行依赖收集
const autoRun = fn => {
	window.__func = fn
	fn()
	window.__func = null
}
```

:::

## 5. Proxy 与 defineProperty 的异同？以及 Vue3 为什么要用 Proxy 替代？

_Proxy 和 defineProperty 都是 JavaScript 中用于拦截和改变对象属性行为的工具_
::: danger Proxy
Proxy 对象用于创建一个对象的代理，从而实现[_基本操作_](https://tc39.es/ecma262/#sec-object-internal-methods-and-internal-slots)的**拦截和自定义**（如属性查找、赋值、枚举、函数调用等）

<p align="right">----摘自MDN官网</p>

可见，相比于 defineProperty，Proxy 可以拦截所有的基本操作，包括*读取原型*和*数组*的相关处理，即回归到了对象的最初状态  
因此，在 Proxy 中重新定义对象的基本操作，即可实现 Vue 中响应式的效果（**依赖收集** 和 **派发更新**）
:::

::: warning defineProperty 是如何处理数组的
上述提到，defineProperty 其实也是对象的*基本操作*之一，因此当数组发生改变时，Vue 无法监听到，也就无法执行相关的响应式操作

因此，在 Vue2 中，对于 data 中定义的数组，修改其继承关系，在数组对象与数组原型之间加入一层自定义对象，在这层对象中重写了数组的全部方法，以此达到监听的效果  
**arr(数组对象) -> 自定义对象(隐式原型对象*proto*指向数组的显式原型) -> Array.prototype**
:::
由此可见，使用 Proxy 使得 Vue3 的响应式更为完整，功能更为强大
