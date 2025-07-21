# DoublyLinkedList 双链表结构

&emsp;&emsp;双链表(DoublyLinkedList)是也一种**链式存储**的数据结构。每个结点的构成：**元素**(数据元素的映象) + **前驱指针**(指示前驱元素存储位置) + **后继指针**(指示后继元素存储位置)
<img src="/DataStructure/DoublyLinkedList.png" />

::: warning
**特点：线性结构，链式存储**

&emsp;核心思想 => 通过改变节点的 **next 指向** 和 **prev 指向** 实现插入、删除的操作

```js
class DoublyNode {
  constructor(element) {
    this.element = element;
    this.next = null;
    this.prev = null;
  }
}

class DoublyLinkedList {
  constructor() {
    this.head = null;
    this.last = null;
    this.length = 0;
  }

  append(element) {
    const node = new DoublyNode(element);
    this.length++;

    if (!this.head) {
      this.head = node;
      this.last = node;
      return;
    }

    this.last.next = node;
    node.prev = this.last;
    this.last = node;
  }

  removeAt(index) {
    if (index < 0 || index >= this.length) return;
    // head.next为空，即链表只有一个元素
    if (this.length === 1) {
      this.head = null;
      this.last = null;
      this.length--;

      return;
    }

    // 删除表头元素
    if (index === 0) {
      this.head = this.head.next;
      this.head.prev = null;

      this.length--;
      return;
    }

    // 删除表尾元素
    if (index === this.length - 1) {
      this.last = this.last.prev;
      this.last.next = null;

      this.length--;

      return;
    }

    const current = this.getNodeByIndex(index);
    current.prev.next = current.next;
    current.next.prev = current.prev;

    this.length--;

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
    // 不存在head，即链表为空
    if (!this.head) {
      this.append(element);
      return;
    }

    const node = new DoublyNode(element);

    // 表头插入
    if (index === 0) {
      const head = this.head;
      head.prev = node;
      node.next = head;
      this.head = node;

      this.length++;

      return true;
    }

    // 表尾插入
    if (index === this.length) {
      const last = this.last;
      last.next = node;
      node.prev = last;
      this.last = node;

      this.length++;

      return true;
    }

    const prev = this.getNodeByIndex(index - 1);
    const current = prev.next;
    current.prev = node;
    prev.next = node;
    node.next = current;
    node.prev = prev;

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

  getLast() {
    return this.last;
  }
}
```

:::
