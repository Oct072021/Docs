# LinkedList 单链表结构

&emsp;&emsp;单链表(LinkedList)是一种**链式存储**的数据结构。链表中的数据是以结点来表示的，每个结点的构成：**元素**(数据元素的映象) + **指针**(指示后继元素存储位置)
<img src="/DataStructure/LinkedList.png" />

::: warning
**特点：线性结构，链式存储**

&emsp;核心思想 => 通过改变节点的 **next 指向**实现插入、删除的操作

```js
class Node {
  constructor(element) {
    this.element = element;
    this.next = null;
  }
}

class LinkedList {
  constructor() {
    this.head = null;
    this.length = 0;
  }

  append(element) {
    const node = new Node(element);
    this.length++;

    if (!this.head) {
      this.head = node;
      return;
    }

    let current = this.head;
    while (current.next) {
      current = current.next;
    }
    current.next = node;
  }

  removeAt(index) {
    if (index < 0 || index >= this.length) return;

    this.length--;

    if (index === 0) {
      this.head = this.head.next;
      return;
    }

    const prev = this.getNodeByIndex(index - 1);
    const current = prev.next;
    prev.next = current.next;

    return current.element;
  }

  getNodeByIndex(index) {
    if (index < 0 || index >= this.length) return;

    let current = this.head;
    for (let i = 0; i < index; i++) {
      current = current.next;
    }

    return current;
  }

  findIndex(element) {
    let current = this.head;
    for (let i = 0; i < this.length; i++) {
      if (current.element === element) return i;
      current = current.next;
    }
    return -1;
  }

  remove(element) {
    const index = this.findIndex(element);
    if (index === -1) return;

    const removeElement = this.removeAt(index);
    this.remove(element);

    return removeElement;
  }

  insert(element, index) {
    if (index < 0 || index > this.length) return;
    const node = new Node(element);

    if (index === 0) {
      const head = this.head;
      node.next = head;
      this.head = node;

      this.length++;

      return true;
    }

    const prev = this.getNodeByIndex(index - 1);
    const current = prev.next;
    node.next = current;
    prev.next = node;

    this.length++;

    return true;
  }

  size() {
    return this.length;
  }

  isEmpty() {
    return this.size() === 0;
  }

  getHead() {
    return this.head;
  }
}
```

:::

:::tip Array 和 LinkedList 的区别
数组(Array)的存储方式为**顺序存储**，利用索引 index 作为标识
链表(LinkedList)的存储方式为**链式存储**，利用**指针**连接每个元素

Array 通过 _index_ 检索元素，因此查询效率更高，时间复杂度为 O(1);但修改(插入和删除)效率低，需要改变后续元素的 index，时间复杂度为 O(n)  
LinkedList 通过 _指针_ 检索元素，因此修改效率更高，只需改变一个元素的指针，时间复杂度为 O(1);但查询的效率低，需要遍历整个结构，时间复杂度为 O(n)  
:::
