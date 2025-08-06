## 1. 虚拟列表的组件封装

> **原理**
>
> - 设置一个**可视区域**，然后用户在滚动列表的时候，本质上是**动态修改可视区域里面的内容**
>
> **技术细节**
>
> 动态修改可以采用动态截断数据的操作来实现，因此需要得到一些信息：
>
> 1. 可视区域**起始数据索引(startIndex)**
> 2. 可视区域**结束数据索引(endIndex)**
> 3. 可视区域的数据，利用上述的startIndex和endIndex**截断**
> 4. 整个列表中的**偏移位置 startOffset**

### 简单实现：假设每一项定高

如下图所示：

<img src="/VirtualList/1.png" style="zoom:50%;" />

整个虚拟列表的设计如下：

```html
<!-- 可视区域容器 -->
<div class="infinite-list-container">
  <!-- 这是容器里面的占位，高度是总列表高度，用于形成滚动条 -->
  <div class="infinite-list-phantom"></div>
  <!-- 列表项渲染区域 -->
  <div class="infinite-list">
    <!-- item-1 -->
    <!-- item-2 -->
    <!-- ...... -->
    <!-- item-n -->
  </div>
</div>
```

- infinite-list-container: 可视区域容器
- infinite-list-phantom： 这是容器里面的占位，高度是总列表高度，用于形成滚动条
- infinite-list：列表项渲染区域

如下图所示：

<img src="/VirtualList/2.png" style="zoom:50%;" />

接下来监听 infinite-list-container 的 scroll 事件，获取滚动位置的 scrollTop

- 假定可视区域高度固定，称之为 screenHeight
- 假定列表每项高度固定，称之为 itemSize
- 假定列表数据称之为 listData
- 假定当前滚动位置称之为 scrollTop

那么我们能够计算出这么一些信息：

1. 列表总高度 listHeight = listData.length * itemSize
2. 可显示的列表项数 visibleCount = Math.ceil(screenHeight / itemSize)
3. 数据的起始索引 startIndex = Math.floor(scrollTop / itemSize)
4. 数据的结束索引 endIndex = startIndex + visibleCount
5. 列表显示数据为 visibleData = listData.slice(startIndex, endIndex)

当发生滚动之后，由于渲染区域相对于可视区域发生了偏移。我们需要计算出来这个偏移量，然后使用 transform 将 list 重新移回到可视区域。

偏移量 startOffset = scrollTop - (scrollTop % itemSize)

<img src="/VirtualList/3.png" style="zoom:50%;" />
