---
title: DOS命令创建文件
date: 2018-03-27 16:54:09
tags: [冷宫, DOS命令]
categories: [技术, 冷宫]
---

### 前言

​    DOS命令，是指DOS操作系统的命令，是一种面向磁盘的操作命令，主要包括目录操作类命令、磁盘操作类命令、文件操作类命令和其它命令。

### 创建文件夹 

```javascript
md [盘符:\][路径\]新文件夹名

如：md c:\test\myfolder 
没写盘符及路径默认在当前路径下创建 `mkdir 文件夹名`也可创建 
md的全称：make directory
```

### 删除文件夹

```javascript
'rd或rmdir' '/s /q [盘符:\][路径\]文件夹名' 
rd只能删除空文件夹 加上'/s'即可直接删除非空文件夹
rd在删除文件夹时会提示是否确定删除 加上'/q'即quiet 无需提示直接删除
```

### 创建文件

```javascript
type nul>'文件名' 创建一个空文件
'echo' '要添加内容'>'文件名'
```

### 删除文件

```javascript
del '文件名'
```

