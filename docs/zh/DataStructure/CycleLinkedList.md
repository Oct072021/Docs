# CycleLinkedList 循环链表结构

&emsp;&emsp;循环链表(CycleLinkedList)是也一种**链式存储**的数据结构。每个结点的构成：**元素**(数据元素的映象) + **指针**(指示后继元素存储位置，且最后的指针指向第一个元素)
<img src="/DataStructure/CycleLinkedList.png" />

::: warning
**特点：线性结构，链式存储**

&emsp;核心思想 => 通过改变节点的 **next 指向** 实现插入、删除的操作(_注意：最后的指针需要指向第一个元素_)

```js
class CycleNode {
  constructor(element) {
    this.element = element;
    this.next = null;
  }
}

class CycleLinkedList {
  constructor() {
    this.head = null;
    this.length = 0;
  }

  append(element) {
    const node = new CycleNode(element);

    if (!this.head) {
      this.head = node;
      node.next = this.head;

      this.length++;

      return;
    }

    const last = this.getNodeByIndex(this.length - 1);
    last.next = node;
    node.next = this.head;

    this.length++;

    return;
  }

  removeAt(index) {
    if (index < 0 || index >= this.length) return;
    // 只有一个元素
    if (this.length === 1) {
      this.head = null;
      this.length--;

      return;
    }

    if (index === 0) {
      this.head = this.head.next;

      const last = this.getNodeByIndex(this.length - 1);
      last.next = this.head;

      this.length--;

      return;
    }

    const prev = this.getNodeByIndex(index - 1);
    const current = prev.next;
    prev.next = current.next;

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

    const node = new CycleNode(element);

    if (index === 0) {
      const head = this.head;
      const last = this.getNodeByIndex(this.length - 1);

      node.next = head;
      this.head = node;

      last.next = this.head;

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
