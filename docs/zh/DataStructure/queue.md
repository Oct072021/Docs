# Queue 队列结构

&emsp;&emsp;队列(Queue)是一种特殊的*线性表*，只允许在**头部**进行**删除**操作，在**尾部**进行**插入**操作
<img src="/DataStructure/Queue.png" />

::: warning
**特点：先进先出 First In First Out(FIFO)**

传统方式：数组具有操作头尾的功能，因此使用数组封装  
缺点：若删除头一个元素，则后续元素都会往前移动一次，因此变相*改变*了整个数组的*内存结构*，存在性能问题

&emsp;鉴于队列的只允许在头尾操作的特性，因此封装过程中利用此特性，给头尾元素打上标识，即可实现头尾的操作  
&emsp;封装思路 =>使用对象结构进行封装，定义两个变量(Number 类型)记录头尾元素的 key 值(**head** 和 **last**)，进队时 last+1，出队时 head+1

```js
class Queue {
	#items
	#last
	#head
	constructor(obj = {}) {
		this.#items = obj
		this.#last = 0 // 队尾
		this.#head = 0 // 队头
	}

	dequeue() {
		if (this.isEmpty()) return
		const val = this.#items[this.#head]
		delete this.#items[this.#head]
		this.#head++
		return val
	}

	enqueue(data) {
		this.#items[this.#last] = data
		this.#last++
	}

	front() {
		return this.#items[this.#head]
	}

	isEmpty() {
		return this.size() === 0
	}

	size() {
		return this.#last - this.#head
	}

	clear() {
		this.#items = {}
	}

	toString() {
		let str = ''
		Object.keys(this.#items).forEach(item => {
			str += this.#items[item]
		})

		return str
	}
}
```

::: danger 举例：击鼓传花游戏

```js
/**
 * @param {Number} num:传花次数
 */
function game(num, ...args) {
	const queue = new Queue_2()
	args.forEach(arg => {
		queue.enqueue(arg)
	})

	const times = num > queue.size() ? num % queue.size() : num
	for (i = 0; i < times; i++) {
		queue.enqueue(queue.dequeue())
	}

	const looser = queue.dequeue()
	console.log(looser, '淘汰了')
	const newArgs = args.filter(arg => {
		if (arg === looser) return false
		return true
	})

	if (newArgs.length === 1) {
		console.log(...newArgs, '获胜！')
		return
	}
	game(7, ...newArgs)
}
```

:::

:::tip Array 和 Object
数组(Array)的存储方式为线性存储，即**有序的数据集合**  
对象(Object)的存储方式为非线性存储，即**无序的数据集合**
:::
