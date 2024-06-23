# Stack 栈结构

&emsp;&emsp;栈(Stack)是一种运算受限的**线性表**，仅限定在**栈顶进行插入和删除**的线性表
<img src="/DataStructure/Stack.png" />

::: warning
**特点：后进先出 Last In First Out(LIFO)**

```js
class Stack {
	#items
	constructor(arr = []) {
		this.#items = arr
	}

	pop() {
		return this.#items.pop()
	}

	push(data) {
		this.#items.push(data)
	}

	peek() {
		return this.#items.at(-1)
	}

	isEmpty() {
		return this.#items.length === 0
	}

	size() {
		return this.#items.length
	}

	clear() {
		this.#items = []
	}

	toString() {
		return this.#items.join('')
	}
}
```

::: danger 举例: 使用 stack 结构作进制转换

```js
const stack = new Stack()

/**
 * 进制转换
 * @param {number} DEC:基数
 * @param {number} base:进制
 * @returns {String}
 */
function convert(DEC, base) {
	let number = DEC
	let target = ''
	const baseString = '0123456789ABCDEF'

	while (number > 0) {
		stack.push(number % base)
		number = Math.floor(number / base)
	}

	while (!stack.isEmpty()) {
		target += baseString[stack.pop()]
	}

	return target
}
```

:::
:::
