---
title: yarn常见问题记录总结
date: 2021-05-31 15:12:33
tags: yarn
categories: [技术]
---

### 前言

​	最近使用yarn安装依赖时一直报`error Command failed`错误，却不知道如何解决，google的解决方案大多将下载源切换成`yarn config set registry https://registry.npm.taobao.org`，尝试后并没有解决我的问题（难受鸭）。一直觉得可能是缓存导致，最后也是使用`yarn cache clean`命令清除缓存后，问题得到解决（开心），也正是该问题的出现，才促使自己对yarn的使用做记录总结（emm比较懒）。

### 常用命令

#### 创建工程

```js
yarn init // 创建 package.json (npm init也阔以)
```

#### 添加/移除依赖

```js
yarn / yarn install							// 	添加全部依赖
yarn add <packageName>
yarn add <packageName> --dev  // devDependencies开发依赖
yarn add <packageName> global // 添加全局依赖
yarn remove <packageName>     // 移除依赖
// 注：yarn add <packageName> --registry=https://registry.yarnpkg.com //指定下载地址（如果不指定，yarn-lock上包的下载库，可能是其他地址）
```

#### yarn 配置

```js
yarn config list  				 // 显示当前配置
yarn config set key value  // 添加/修改配置
yarn config delete key		 // 删除配置
yarn config get key 			 // 获取配置
// yarn config set registry  https://registry.npm.taobao.org （设置淘宝镜像）
```



### 不常用命令

#### 缓存

```js
yarn cache
yarn cache list // 列出已缓存的每个包
yarn cache dir  // 返回全局缓存位置
yarn cache clean// 清除缓存
```

### 问题

缓存导致报错`error Command failed`

```js
yarn cache clean // 清除缓存
```

