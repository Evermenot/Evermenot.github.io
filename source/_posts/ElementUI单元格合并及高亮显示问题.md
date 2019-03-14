---
title: ElementUI单元格合并及高亮显示问题
date: 2018-05-21 15:21:06
tags: [element-ui, vue]
categories: [技术, vue]
---

### 前言

​	[原博客地址](https://www.cnblogs.com/Evermenot/p/9057744.html)

​	在使用element-ui 的表格式涉及到了单元格合并问题，实际工作中数据多是从后台获取的，很显然数据不是一成不变的，所以就要根据数据的变化动态的合并单元格，动态动态动态，我TM不会啊~。~，尝试合并一直出现这样那样的问题，不会还这么多借口\~~渣渣\~~

​	去问了下度娘，发现类似问题不是很多，这特么不正常啊，果然是我太渣o(╥﹏╥)o ，但总算找到两个总结记录下吧，万一忘了~~~~	好了，切入正题：

### 效果（一图胜百言）

![img](/images/elementui/span.webp)

### 单元格合并

#### **场景：**

​	例如某个版本下对应多行数据，这就不免就涉及到了单元格合并

#### **大致思路：**

1. 以版本段为例（每个版本下有多条数据），为每一个版本下的 每一行 数据中都添加上对应的版本数据（版本字段）
2. 设定一个数组来存放要合并的格数，同时还要设定一个变量来记录，当版本变化时数据的索引
3. 遍历表格数据

#### UI层

```javascript
// span-method绑定的为合并单元格的方法
<el-table :data="coreTable" :span-method="retenSpan"></el-table>
```

#### 逻辑层

##### 处理数据（重点）

```javascript
// 设定一个数组来存放要连续合并的格数，同时还要设定一个变量来记录，当时间段不同时数据的索引
let spanArr = [], contactDot = 0,
retenTable.forEach((item, index) => {
    if(index === 0){
        spanArr.push(1);
    }else {
        if(item.version === retenTable[index - 1].version){
            spanArr[contactDot] += 1;
            spanArr.push(0);
        }else {
            spanArr.push(1);
            contactDot = index;
        }
    }
})
this.spanArr = spanArr;
```

##### 合并

```javascript
// 单元格合并
retenSpan({ row, column, rowIndex, columnIndex }) {
    if(columnIndex === 0){
        const _row = this.spanArr[rowIndex]
        const _col = _row > 0 ? 1 : 0;
        return {
            rowspan: _row,
            colspan: _col
        }
    }
},
```

### 高亮显示

#### 场景：
合并完单元格后，假定此时某个版本对应的多行数据，鼠标放上其中的某一行时，只是该行高亮显示，而不是高亮显示所有合并行（所以要解决下）

#### 	UI层

```javascript
// 绑定element-ui里的鼠标进入和离开单元格的方法
<el-table  
    :data="coreTable" 
    :span-method="retenSpan" 
    :row-class-name="tableRowClassName"
    @cell-mouse-leave="cellMouseLeave" @cell-mouse-enter="cellMouseEnter">
</el-table>
```

#### 逻辑层

##### 处理数据（重点）

```javascript
//  遍历表格数据，为每一条数据添加一个index属性用来标识每行数据，最后输出结果格式为 sameRowArr = [[0, 1, 2, 3], [4, 5, 6], [7, 8, 9],......]
//　sameRowArr中的每一项代表，在一个条件下的数据（如同一个版本等），代码中的sIdx是用来控制数组的下标，版本变化时sIdx +1
let sameRowArr = [], sIdx = 0;
retenTable.forEach((item, index) => {
    item.index = index
    if(index === 0){
        sameRowArr.push([index])
    }else {
        if(item.version === retenTable[index - 1].version){
            sameRowArr[sIdx].push(index)
        }else {
            sIdx = sIdx + 1;
            sameRowArr.push([index])
        }
    }
})
this.sameRowArr = sameRowArr;
```

##### 添加类名

鼠标移上及移除即可实现动态添加及移除类名，从而实现合并行高亮效果

大致思路：

- 鼠标移入，在cellMouseEnter中，遍历sameRowArr，使用事先存好的索引，判断鼠标移入行的索引是属于sameRowArr中的哪一子项
- 将sameRowArr的子数组赋值给一个全局数组curRowArr
- 在tableRowClassName方法中，循环curRowArr数组
- 行号与子数组中存的行数相同，为该行添加同个类名

```javascript
// row_class为定义好的css样式类名
tableRowClassName({row, rowIndex}){
    // 同一行
    let temArr = this.curRowArr
    for(let i = 0; i < temArr.length; i++) {
        if(rowIndex == temArr[i]) {
            return 'row_class'
        }
    }
},
cellMouseEnter(row, column, cell, event) {
    this.sameRowArr.forEach((arr, i) => {
        if(arr.indexOf(row.index) != -1){
            this.curRowArr = arr
        }
    })
},
cellMouseLeave(row, column, cell, event) {
    this.curRowArr = []
},
```

