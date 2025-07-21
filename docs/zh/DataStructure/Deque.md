# Deque 双端队列结构

&emsp;&emsp;双端队列(Double-Ended Queue)是一种具有**队列和栈**的性质的数据结构。双端队列中的元素可以从**头尾**两端进行操作(入队和出队)
<img src="/DataStructure/Deque.png" />

::: warning
**特点：可以看作是两个 Stack 结构组成的 Queue 结构**

封装思路

<div class="container">
  <span>head =></span>
  <div>
    <div>
      <text>出队</text>操作时与Queue操作相同，<text>入队</text>操作时需要判断：<br>
      &emsp;1.为空则等同于在尾部入队；<br>
      &emsp;2.#head>0则#head-1后插入；<br>
      &emsp;3.若都不满足则需要把所有元素的key值+1，再在头部插入元素
    </div>
  </div>
</div>

<div class="container">
  <span>last =></span>
  <div>
    <div>
      <text>入队</text>操作时与Queue操作相同，<text>出队</text>操作时#last-1即可
    </div>
  </div>
</div>

<style>
  .container {
    display: flex;
    align-items: center;

    span {
      width: 60px;
    }

    text {
      font-weight: 900;
    }
  }
</style>

```js
class Deque {
  #items;
  #head = 0;
  #last = 0;
  constructor(items = {}) {
    this.#items = items;
  }

  // 与Queue相同
  removeFront() {
    if (this.isEmpty()) return;
    const val = this.#items[this.#head];
    delete this.#items[this.#head];
    this.#head++;
    return val;
  }

  removeEnd() {
    if (this.isEmpty()) return;
    this.#last--;
    const val = this.#items[this.#last];
    delete this.#items[this.#last];
    return val;
  }

  addFront(data) {
    // 1.如果队列为空
    if (this.isEmpty()) {
      this.addEnd(data);
      return;
    }

    // 2.如果刚从队头删除完元素(即 #head > 0),则可在删除的位置加入
    if (this.#head > 0) {
      this.#head--;
      this.#items[this.#head] = data;
      return;
    }

    // 3.如果没有进行任何删除操作,则需要把所有元素往后移一位
    for (let i = this.#last; i > 0; i--) {
      this.#items[i] = this.#items[i - 1];
    }

    this.#items[this.#head] = data;

    this.#last++;
  }

  // 与Queue相同
  addEnd(data) {
    this.#items[this.#last] = data;
    this.#last++;
  }

  peekFront() {
    return this.#items[this.#head];
  }

  peekEnd() {
    return this.#items[this.#last - 1];
  }

  isEmpty() {
    return this.size() === 0;
  }

  size() {
    return this.#last - this.#head;
  }

  clear() {
    this.#items = {};
  }

  toString() {
    let str = "";
    Object.keys(this.#items).forEach((item) => {
      str += this.#items[item];
    });

    return str;
  }
}
```

::: danger 举例：判断回文

```js
/**
 * @param {String} str:要判断的字符串
 */
function check(str) {
  const formatStr = str.toLowerCase().split(" ").join("");

  const deque = new Deque();

  Array.from(formatStr).forEach((str) => {
    deque.addEnd(str);
  });

  let isEuqal = true;
  while (deque.size() > 1) {
    if (deque.removeFront() !== deque.removeEnd()) {
      isEuqal = false;
      break;
    }
  }

  return isEuqal;
}
```

:::

:::tip
尽管双端队列看起来似乎比栈和队列更灵活，但实际上在应用程序中远不及栈和队列有用
:::
