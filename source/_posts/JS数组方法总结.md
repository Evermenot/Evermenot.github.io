---
title: JS数组方法总结
date: 2019-02-22 18:21:18
tags: JavaScript
categories: [技术, JavaScript]
---

### 前言

​	在日常工作中，时常使用JS数组中的方法来处理后台接口传回来的数据，作为一枚前端菜鸡，对于这些方法虽然有些了解，但有些并没有去尝试使用`（常常使用某一两种方法处理数据，很单一甚至笨拙）`，同时对于它们之间的细微差别也没有多少了解，或匆匆了解后，没有整理并记录下来，最后只混了个面熟~.~...而这就是本篇博客要解决的痛点！

​	除了整理记录外，最终的目的还是为了提高开发效率，在合适的场景使用合适的方法~

​	`注：发现一些不一样的东西，感觉还挺新奇有趣的~~~em...拥抱变化`

知乎上看见的一张图片（挺赞的~）

![img](/images/array/array.png)

### sort( ) 

```javascript
1.作用：对数组的元素进行排序
2.语法：arrayObject.sort(sortby)     参数可选,且必须为函数

3.实例：
     实例1：不传参情况，该方法默认将数组元素转换成字符串，按照字符编码顺序进行排序
     实例2：传入参数
            var arr = new Array(5,2,4,1,3);
            function sortbyArgs(a, b) {
              return a - b;
            }
            var result = arr.sort(sortbyArgs);
            console.log(result)    // 结果： 1 2 3 4 5  (默认从小到大排序)
            
      注：如果将返回值改为 b - a  将按照从大到小排序  (结合下面可理解原因)
      
4.原理: 参数 a 和 b 在调用时相当于取出数组的前两个值, sort方法根据传入的参数函数返回值的          
         正负或者0,来判断如何排序类似于冒泡排序.
         默认a - b < 0 时,a和b不交换位置(即a还在b前);
         a - b > 0 时,a和b交换位置(a换到b后);
```

### concat( )

```javascript
1.作用：连接两个或更多的数组，并返回新数组。
2.语法：arrayObject.concat(arrayX,arrayX,...,arrayX)    arrayX必需,可为任意多个

3.实例：
	let aa = [[[10,11,12],5,6],1,2,3], bb = [7,8,9];
	let cc = aa.concat(bb) // [[[10,11,12],5,6],1,2,3,7,8,9]
    cc[0][1] = 55 // aa: [[[10,55,12],5,6],1,2,3]
注：concat会将对象引用复制到新数组中，如果引用的对象被修改，则新数组和原始数组都会被更改
换句话说：对于新数组的任何操作（仅当元素不是对象引用时）都不会对原始数组产生影响，反之亦然。
```

### indexOf 和 includes 

```javascript
1.indexOf
	语法：array.indexOf(val, startIndex)
	优点：返回元素的下标（首次出现的位置），不存在返回-1
	缺点：无法判断是否有NaN元素

2.includes // es6新增
	语法：array.includes(val, startIndex) // 返回Boolean值
	优点：可判断NaN元素
	缺点：a.无法获取元素的下标;
		 b.空值会被解析为undefined
            ss = [ , 2, 3]
            ss.includes(undefined)// true
效率：includes > indexOf
```

### find( )

```javascript
1.作用：返回数组中满足测试函数的第一个元素值，否则返回undefined。
2.语法：arr.find(callback[, thisArg])
3.实例：
	var inventory = [
    	{name: 'apples', quantity: 2},
    	{name: 'cherries', quantity: 5}
	];
	function findCherries(fruit, index, arr) { 
    	return fruit.name === 'cherries';
	}
	console.log(inventory.find(findCherries)); 
	// { name: 'cherries', quantity: 5 }
```

### findIndex( )

```javascript
// 和find类似
作用：返回满足条件的第一个元素的索引，否则返回-1
注：可判断NaN元素，感觉是对indexOf的补充
```



### entries( )

```javascript
// es6新增（实用性不大了解即可）
1.作用：返回一个新的Array Iterator对象，该对象包含数组中每个索引的键值对。
2.语法：arr.entries()

3.实例：
	var arr = ["a", "b", "c"];
	var iterator = arr.entries();
	console.log(iterator.next());
注：Array Iterator对象原型上有一个next方法，可用于遍历迭代器取得原数组的[key,value]。
个人看法：感觉数组的entries方法，没有对象的Object.entries()实用，比如：使用接口参数params对象拼接表格按钮的下载链接（entries() 结合 for...of 使用）
```

### every( )

```javascript
// 满足所以才为真
1.作用：测试数组的所有元素是否都通过callback函数的测试，返回Boolean值。
2.语法：arr.every(callback[, thisArg])
	callback：用来测试每个元素的函数；
	thisArg：执行callback时使用的 this 值。

3.实例：
	function isBelow(curVal) {
      	console.log(curVal) // 1 30 29
        //未被赋值的元素将不会执行callback函数。
  		return curVal < 40;
	}
	var arr = [1, 30, , 29]; // true 
	console.log(arr.every(isBelow));
注：应用场景：判断数组元素的区间、判断元素是否都为某一类型等
	callback 被调用时传入三个参数：元素值curVal，元素的索引index，原数组arr。
```

### some( )

```javascript
// 满足一个即为真
1.作用：测试是否至少有一个元素通过callback函数的测试，返回Boolean值。
2.语法：arr.some(callback[, thisArg])

3.实例：
	function isAbove(curVal) {
      	console.log(curVal) // 1 30 29
        //未被赋值的元素将不会执行callback函数。
  		return curVal > 40;
	}
	var arr = [1, 30, , 49]; // true 
	console.log(arr.every(isAbove));
注：应用场景：判断数组元素的区间、判断元素是否存在某一类型等
	callback 被调用时传入三个参数：元素值curVal，元素的索引index，原数组arr。
```

### filter( )

```javascript
1.作用：返回一个新数组, 其包含通过所提供函数实现的测试的所有元素。
2.语法：arr.filter(callback[, thisArg])

3.实例：
	function isBig(curValcurVal) {
  		return curVal >= 10;
	}
	var filtered = [12, 5, 8, 130, 44].filter(isBig);
	// filtered is [12, 130, 44]
注：filter方法使用场景超多，脑补
```

### map( )

```javascript
1.作用：返回一个新数组，数组中的元素为原始数组元素调用函数处理后的值。
2.语法：array.map(callback[, thisArg])
	callback：用来测试每个元素的函数；
	thisArg：执行callback时使用的 this 值。

3.实例：
	let r = data.map(item => {
    	return {
        	title: item.name,
        	sex: item.sex === 1? '男':item.sex === 0?'女':'保密',
        	age: item.age,
        	avatar: item.img
    	}
	})
注：工作中经常使用到map方法，来对数据进行二次处理（处理完当前元素记得再return出去，例如：对象）
```

