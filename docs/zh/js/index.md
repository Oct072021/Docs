## 1. 对 JS 垃圾回收机制的理解

::: tip
原理：找到那些不再继续使用的变量，然后释放其占用的内存。垃圾收集机制会按照固定的时间间隔周期性地执行这一操作
:::

最常用的跟踪方法是标记清除，当函数执行完后，就会给局部变量打上“离开环境”的标记（除了闭包），在下一次垃圾回收时间到来时就会清除这一块内存，手动将一个有值的变量赋值为 null，也是让这个值离开环境，也可以释放内存

## 2. 事件委托原理

利用事件冒泡，让子元素所执行的事件，让其父元素代替执行

## 3. null 和 undefined 的区别

::: tip
null 是一个表示"无"的对象，转为数值时为 0；undefined 是一个表示"无"的原始值，转为数值时为 NaN  
当声明的变量还未被初始化时，变量的默认值为 undefined。 null 用来表示尚未存在的对象  
:::

**undefined** 表示"缺少值"，就是此处应该有一个值，但是还没有定义
::: tip 典型用法
（1）变量被声明了，但没有赋值时，就等于 undefined  
（2）调用函数时，应该提供的参数没有提供，该参数等于 undefined  
（3）对象没有赋值的属性，该属性的值为 undefined  
（4）函数没有返回值时，默认返回 undefined
:::
**null** 表示"没有对象"，即该处不应该有值
::: tip 典型用法
（1） 作为函数的参数，表示该函数的参数不是对象  
（2） 作为对象原型链的终点
:::

## 4. new 操作符具体做了什么

1、创建一个空对象，并且 this 变量引用该对象，同时还继承了该函数的原型  
2、属性和方法被加入到 this 引用的对象中  
3、新创建的对象由 this 所引用，并且最后隐式的返回 this

## 5. 谈谈闭包

首先闭包是函数内部再嵌套一层函数，并且嵌套的函数必须要使用到外层函数定义的变量，当执行外层函数时，闭包形成。
::: tip 核心作用
使变量可以保留在内存中，不被释放掉。也因为如此，可能会造成内存泄漏
:::

## 6. 谈谈 Promise

（1）promise 是为解决异步处理回调地狱问题而产生的  
（2）有三种状态，pengding、resolve、reject，状态一旦决定就不会改变  
（3）then 接收 resolve()，catch 接收 reject()

## 7.JS 判断数据类型

::: tip typeof
能判断出四种，分别是 number，string，boolean，object，剩余的均被检测为 object
:::
::: tip instanceof
判断参照对象的 prototype 属性所指向的对象是否在被行测对象的原型链上
:::
::: tip constructor
针对于 instanceof 的弊端，我们使用 constructor 检测，constructor 是原型对象的属性指向构造函数  
 这种方式解决了 instanceof 的弊端,可以检测出除了 undefined 和 null 的 9 种类型
:::
::: tip Object.prototype.toString.call
Object.prototype.toString 可以取得对象的内部属性[[class]]，并根据这个内部属性返回诸如"[object Number]"的字符串，那么我们就可以通过 call 获取内部属性[[class]]
:::

## 8.TS 中 type 和 interface 的异同

::: tip 相同点

1. 都可以描述一个对象或函数
2. 都允许扩展(extends),interface 使用 extends，type 使用等号（=）  
   :::

   ::: tip 不同点

3. type 可以声明基本类型别名，联合类型，元组等，interface 则不能
4. interface 可以合并（覆写），type 不能
   :::

## 9.call、bind、apply 的作用和区别

::: tip

```js
fn.apply(this, [args_array])
fn.call(this, arg1, arg2, ...)

fn.bind(this, arg1, arg2, ...)()
or
const newFn = fn.bind(this, arg1, arg2, ...)
```

&emsp;call 和 apply  
都可以改变 this 的指向，并且改变后**立即执行**，因此是**临时改变**  
但传参方式有所不同  
&emsp;bind  
bind 的返回值是一个函数，此函数已经改变了 this 指向，因此是**永久更改**  
不会立即执行，需要手动调用
:::

## 10.谈谈 ES6 的箭头函数

消除了普通函数的**二义性**
::: tip 普通函数
既可以通过 () 执行调用，也可以通过 new 调用  
因此意义不明，可读性差

this：有自己的 this 指向，指向**调用者**，即**存在原型**

:::
::: warning 箭头函数
无法通过 new 调用，只能作为函数**执行调用**，意义明确

this：没有自己的 this，使用的是**父级作用域的 this**，即**不存在原型**
:::

## 11. js 高阶函数? 函数柯里化？

::: danger 函数的本质
_对流程的封装，使其具有通用性_
:::
先看一个普通的例子，createMap()作用是把数组的每一项翻指定的倍数

```js
const arr1 = [1, 2, 3, 6]

function createMap(arr, rate) {
	const newArr = []
	arr.forEach(item => {
		newArr.push(item * rate)
	})

	return newArr
}

console.log(createMap(arr1, 2))
// [ 2, 4, 6, 12 ]
```

假如需要把数组的每一项转换成指定的格式，比如{ name: name[item] }，显然 rate 不适用

<h1 align="center">↓</h1>

```js{4}
function createMap(arr, mapper) {
	const newArr = []
	arr.forEach(item => {
		newArr.push(mapper(item))
	})

	return newArr
}

console.log(createMap(arr1, n => n * 2)) // [ 2, 4, 6, 12 ]
console.log(createMap(arr1, n => ({ name: `name${n}` })))
// [
//   { name: 'name1' },
//   { name: 'name2' },
//   { name: 'name3' },
//   { name: 'name6' }
// ]
```

<h1 align="center">↓</h1>
<h4 align="center">函数柯里化写法</h4>

```js{2-8}
function createMap(arr) {
	return function (mapper) {
		const newArr = []
		arr.forEach(item => {
			newArr.push(mapper(item))
		})

		return newArr
	}
}

const newArr = createMap(arr1)(n => ({ id: n }))
console.log(newArr) // [ { id: 1 }, { id: 2 }, { id: 3 }, { id: 6 } ]
```

**柯里化是一种将使用多个参数的一个函数转换成一系列使用一个参数的函数的技术**
::: warning 实现思路
判断当前传入函数的参数个数 (args.length) 是否大于等于原函数所需参数个数 (fn.length) ，如果是，则执行当前函数；如果是小于，则返回一个函数  
:::

```js
function curry(fn, ...args) {
	return args.length >= fn.length ? fn(...args) : (..._args) => curry(fn, ...args, ..._args)
}
```

::: tip 函数柯里化的优点

&emsp;&emsp;函数更加灵活和可重用。通过柯里化，可以将一个多参数的函数转换为一系列单参数的函数，使函数更加灵活和可重用  
&emsp;&emsp;可以避免重复的代码。通过柯里化，可以避免在调用函数时重复地传递参数，从而避免了重复的代码
:::

::: tip 函数柯里化的缺点

&emsp;&emsp;可能会降低性能。通过柯里化，函数的性能可能会降低，因为需要额外的内存来存储函数的返回值和参数  
&emsp;&emsp;可能会增加代码复杂度。通过柯里化，可能会增加代码的复杂度，因为需要处理额外的参数和函数返回值
:::
