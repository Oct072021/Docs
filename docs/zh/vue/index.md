# 1. 对 JS 垃圾回收机制的理解

::: info
原理：找到那些不再继续使用的变量，然后释放其占用的内存。垃圾收集机制会按照固定的时间间隔周期性地执行这一操作
:::

最常用的跟踪方法是标记清除，当函数执行完后，就会给局部变量打上“离开环境”的标记（除了闭包），在下一次垃圾回收时间到来时就会清除这一块内存，手动将一个有值的变量赋值为 null，也是让这个值离开环境，也可以释放内存

# 2. 事件委托原理

利用事件冒泡，让子元素所执行的事件，让其父元素代替执行

# 3. null 和 undefined 的区别

::: tip
null 是一个表示"无"的对象，转为数值时为 0；undefined 是一个表示"无"的原始值，转为数值时为 NaN  
当声明的变量还未被初始化时，变量的默认值为 undefined。 null 用来表示尚未存在的对象  
:::

**undefined** 表示"缺少值"，就是此处应该有一个值，但是还没有定义
::: details 典型用法
（1）变量被声明了，但没有赋值时，就等于 undefined  
（2）调用函数时，应该提供的参数没有提供，该参数等于 undefined  
（3）对象没有赋值的属性，该属性的值为 undefined  
（4）函数没有返回值时，默认返回 undefined
:::
**null** 表示"没有对象"，即该处不应该有值
::: details 典型用法
（1） 作为函数的参数，表示该函数的参数不是对象  
（2） 作为对象原型链的终点
:::

# 4. new 操作符具体做了什么

1、创建一个空对象，并且 this 变量引用该对象，同时还继承了该函数的原型  
2、属性和方法被加入到 this 引用的对象中  
3、新创建的对象由 this 所引用，并且最后隐式的返回 this

# 5. 谈谈闭包

首先闭包是函数内部再嵌套一层函数，并且嵌套的函数必须要使用到外层函数定义的变量，当执行外层函数时，闭包形成。
::: tip 核心作用
使变量可以保留在内存中，不被释放掉。也因为如此，可能会造成内存泄漏
:::

# 6. 谈谈 Promise

（1）promise 是为解决异步处理回调地狱问题而产生的  
（2）有三种状态，pengding、resolve、reject，状态一旦决定就不会改变  
（3）then 接收 resolve()，catch 接收 reject()

# 7.JS 判断数据类型

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

::: info
This is an info box.
:::

::: tip
This is a tip.
:::

::: warning
This is a warning.
:::

::: danger
This is a dangerous warning.
:::

::: details
This is a details block.
:::
