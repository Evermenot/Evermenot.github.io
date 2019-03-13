---
title: web适配相关知识及案例
date: 2019-03-04 17:36:35
tags: [适配, less]
categories: [技术, less]
---

### 前言

​	想象下当你接到美工（呸...UI设计师）的设计稿时，产品&leader让你基于该套设计稿来适配市面上大多数的终端时，内心多少还是有点排斥的吧！要写很多不同终端下的CSS样式代码了呜呜┭┮﹏┭┮，<span style='font-size: 12px;'>（注：反正我是有点排斥写CSS的，这样可能不好~.~）</span>，而基于设计稿采用Less语言批量生成媒体查询做适配的方式，将会减少很多你在页面样式方面的工作量（美滋滋~），下面将介绍下具体的实现，及所涉及知识点的简单介绍。

### Less

#### 介绍

> [less文档](https://less.bootcss.com/)，Less是CSS的扩展语言，本质上还是为了方便编写CSS，使用其写的样式最后仍会被转换成CSS代码（CSS相当于Less的子集）

#### 使用

- Node.js 环境中：

  ```
  npm install -g less	 // 全局安装less
  lessc styles.less styles.css   // 使用less命令生成.css文件
  ```

- VSCode：

  ```
  下载easy less插件创建.less文件，在保存.less文件时会自动生成.css文件
  注：我常使用的IDE就是VSCode，使用它的原因是其可以很方便快捷的下载各种可以提高开发效率的插件，而且很丰富
  ```


<span style='font-size: 12px;'>注：还有些其他的使用方式（自行了解），比如在.vue中的使用（需要加载器）等</span>

#### 功能

Less支持在编写样式时使用：变量、嵌套、混合、函数 & 运算。

混合：

> <span style='font-size: 12px;'>注：只介绍混合的原因，是之前我几乎没使用过它，却是个不错的功能</span>

```css
// LESS
.rounded (@radius: 5px) {
  border-radius: @radius;
}
#header {
  .rounded-corners;
}
#footer {
  .rounded-corners(10px);
}
// 生成的CSS
#header {
  border-radius: 5px;
}
#footer {
  border-radius: 10px;
}
//注：混合功能可以结合less的其他功能一起使用
```

### Media Query

相关文章：

[菜鸟教程](http://www.runoob.com/cssref/css3-pr-mediaquery.html)

[CSS3 Media Queries](https://www.w3cplus.com/content/css3-media-queries)

[media query(媒体查询)和media type(媒体类型)](https://www.cnblogs.com/august-8/p/4537685.html)

#### 定义

> 使用 @media 查询，你可以针对不同的媒体类型定义不同的样式。
>
> <span style='font-size: 12px;'>
>
> 注：@media 可以针对不同的屏幕尺寸设置不同的样式，特别是如果你需要设置设计响应式的页面，@media 是非常有用的。
>
> 当你重置浏览器大小的过程中，页面也会根据浏览器的宽度和高度重新渲染页面。
>
> </span>

#### 语法

```css
@media mediatype and|not|only (media feature) {
    CSS-Code;
}
或
// mediatype 媒体类型
<link rel="stylesheet" media="mediatype and|not|only (media feature)" href="mystylesheet.css">

// 多个媒体类型的CSS规则，可以用逗号来隔开
@media handheld and (min-width:360px),screen and (min-width:480px){
	body{font-size:large;}
}
```

#### 媒体类型

- all：用于所有设备
- print：用于打印机和打印预览
- screen：用于终端设备的屏幕（最常用）
- speech：应用于屏幕阅读器等发声设备

#### 媒体功能（或条件）

<span style='font-size: 12px;'>简单列几个常见的：</span>

- height（min-height，max-height）（浏览器）可视区的高度
- width（min-width，max-width）（浏览器）可视区的宽度
- （max-或min-）device-width、设备实际屏幕的宽度
- （max-或min-）device-height 设备实际屏幕的高度
- resolution：定义设备的分辨率。如：96dpi, 300dpi, 118dpcm

#### 关键字

- and：用来合并多个媒体条件或合并媒体条件与媒体类型。

- not：排除某种制定的媒体类型（即：排除符合表达式的设备）

- only：防止老旧的浏览器不支持带媒体属性的查询而应用到给定的样式

  ```javascript
  <link rel="stylesheet" media="only screen and (min-width:361px) and (max-width:480px)" href="android480.css" type="text/css" />
  // 开始一直没看懂only的具体作用，看了好几遍才懂~~
  1.only用来定某种特定的媒体类型，可以用来排除不支持媒体查询的浏览器
  2.支持媒体特性（Media Queries）的设备，正常调用样式，此时满足条件后only等于不存在；
  3.对于不支持媒体特性但又支持媒体类型(Media Type)的设备，就会不读样式，因为其先读only而不是screen；
  4.另外不支持Media Qqueries的浏览器，不论是否支持only，样式都不会被采用。
  // 总的来说支持媒体查询的，在满足媒体条件下样式即生效
  ```

  ​

#### 使用方式

- link元素

  ```html
  <link rel="stylesheet" media="screen and (min-width: 400px) and (max-width: 800px)" href="style.css" type="text/css" />
  // 一个media query可以包含多个媒体条件
  ```

- @import

  ```css
  @import url("./reset.css") screen;
  ```

- style元素

  ```html
  <style>
      @media only screen and (max-width: 500px) {
          .gridmain {
              width:100%;
          }
      }
  </style>
  ```

### rem

#### 定义

> 相对长度单位，相对于根元素(root element即html元素)font-size值的倍数
>
> 注：根元素html默认font-size: 16px;

#### rem、em、px之间的区别

相关文章：

[px、em、rem的区别](https://www.cnblogs.com/goloving/p/7677236.html)

[px、em、rem区别介绍](http://www.runoob.com/w3cnote/px-em-rem-different.html)

-  px：绝对单位；像素px是相对于显示器屏幕分辨率而言的；

   ```
   注：
   1. px是你屏幕设备物理上能显示出的最小的一个点，这个点不是固定宽度的，不同设备上点的长宽、比例有可能会不同
   2. 显示器1px宽=1毫米 和 显示器1px宽=两毫米，定义一个div宽度为100px，前者显示器div是10厘米，后者显示是20厘米
   3. px的长宽不一定是1:1的正方形
   ```

- em：相对长度单位，相对于父元素的字体大小；

  ```
  浏览器默认（html）font-size: 16px，此时1em=16px，则12px=0.75em，10px=0.625em；
  如果现在body里定义font-size:12px，此时1em=12px，0.8em实际等于12px*0.8；
  如果要改变整个网页字体统一变大变小只须改变body里面font-size的值即可。

  简化font-size换算：
  	在body元素中定义Font-size=62.5%，则1em=16px*62.5%=10px；12px=1.2em；16px=1.6em
  ```

- rem：相对长度单位CSS新增，相对于根元素的字体大小；

  ```
  rem为元素（标签）设定字体大小时，仍然是相对大小，但相对的只是HTML根元素；
  通过它既可以做到只修改根元素就成比例地调整所有字体大小，又可以避免字体大小逐层复合的连锁反应；
  除了IE8及更早版本外，所有浏览器均已支持rem；
  对于不支持它的浏览器，应对方法也很简单，就是多写一个绝对单位的声明。
  这些浏览器会忽略用rem设定的字体大小。
  如：
  p {font-size:14px; font-size:.875rem;}

  简化font-size换算：
  	html的font-size设置成10px，换算时直接将数值除以10在加上rem的单位即可。
  ```


### 案例

#### 基于设计稿采用Less语言批量生成媒体查询做适配

Less代码

```less
// 利用媒体查询结合less需要现设定一个设计稿的大小  如 640px  根元素的font-size=100px
// 然后基于设定的设计稿 来 计算其他屏幕大小下的rem值 
@charset "utf-8";

// 假定设计稿
@designSize: 640px; 

// 基于750设计稿的font size of the root element
@baseSize: 32px;


/*定义变量部分*/
// @adapterDeviceList相当于一个数组
@adapterDeviceList: 320px, 360px, 375px, 480px, 512px, 540px, 600px, 640px, 720px, 750px;

//上面变量的长度
@len: length(@adapterDeviceList);

// mixin混合
.adapterFunc(@index) when (@index <= @len) {
    @media (min-width: extract(@adapterDeviceList, @index)) {
        html {
            font-size: extract(@adapterDeviceList, @index) / @designSize * @baseSize
        }
    }
    .adapterFunc(@index + 1);   // 迭代    less没有遍历语法
    // @index++    错误的不能这样写
}

.adapterFunc(1);
```