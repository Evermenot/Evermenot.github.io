---
title: webpack的基本使用
date: 2018-03-06 18:25:09
tags: webpack
categories: [技术]
---

### 介绍webpack

- 使用场景：

  个人认为webpack的使用场景是在你使用框架，如vue要做一个单页面应用的项目时，各个组件(相当于各个页面)都存在css样式、js逻辑代码、UI层元素。而使用webpack可以达到将各个组件中的css、js、html打包生成对应的单个css、js、html文件

- 好处总结：

  - 能够处理静态资源的依赖关系
  - 能够优化项目代码，比如：压缩合并文件，把图片转为base64编码格式
  - 能够把高级的语法转为低级的语法
  - webpack 能够转换一些模板文件如：'.vue'文件

### 首先创建项目

-  `npm init` （生成package.json文件，记录了项目的基本信息）
-  常用npm命令：
   - npm install vue --save 或 -S		安装到项目本地的生产依赖
   - npm install vue --save-dev 或 -D         安装开发依赖
   - npm install vue --Save-D                       (错误的~~这只能默认装到生产依赖)

### 安装webpack

-  `npm install webpack --save-dev`既要在项目本地安装，
-  全局也要安装目的是在全局使用webpack命令`npm i webpack -g`
-  如果不全局安装webpack那么在使用webpack命令时就要将webpack在node_module中的全部路径在命令行中加上，如：./node_modules/webpack

### 配置webpack

- 新建webpack.config.js配置文件      
- dos命令新建  type nul>webpack.config.js
- 创建完配置文件，在配置文件中导入‘path’模块 
- 再在module.exports中配置打包的入口及出口，
- 最后打包：在控制台输入 webpack  --config  webpack.config.js   生成bundle.js即表示成功
- 基于每次打包都要输入上步过于繁琐的命令，所以可直接在webpack.config.js文件中配置 '打包指令'

```javascript
// webpack.config.js  文件基本配置
// 引入模块
const path = require('path');
// 导入插件（需要在下面配置插件才能生效）
const htmlWebpackPlugin = require('html-webpack-plugin')

// 导出
module.exports = {
    entry: path.join(__dirname, './src/main.js'),
    output: {
        path: path.join(__dirname, './dist'),  // 输出文件名的路径
        filename: 'bundle,js'      // 打包输出文件名
    },

    // 配置插件  如：html-webpack-plugin 要创建插件实例
    plugins: [
        new htmlWebpackPlugin({
        // 复制如下路径的文件为模板托管到内存中   会在index.html文件中自动引入main.js
        template: path.join(__dirname, './src/index.html'),
        filename: 'index.html'
        }),
        ...
    ]

    // 配置加载器规则  如：less-loader即less加载器、style-loader可将各组件的样式加载到页面上
    module: {
        rules: [
            // 如下是几个必须安装的加载器
            { test: /\.css$/, use: ['css-loader']},
            { test: /\.js$/, use: 'babel-loader', exclude: /node_modules/ },
            // babel-loader 加载器可以将es6语法转换为es5的语法
            { test: /\.vue$/, use: 'vue-loader' },
            // 加载'.vue'文件  对应的项目依赖包 vue-template-compiler
        ]
    }
}
```

### webpack 中的 Loader 的作用是什么？

```
1. 实现对不同格式的文件的处理，比如说将 scss 转换为 css，或者 typescript 转化为 js
2. 转换这些文件，从而使其能够被添加到依赖图中
常用的loader：
babel-loader：让下一代的js文件转换成现代浏览器能够支持的JS文件；
babel有些复杂，所以大多数都会新建一个.babelrc 进行配置；
css-loader,style-loader:两个建议配合使用，用来解析 css 文件，能够解释@import,url()如果需要解析 less 就在后面加一个 less-loader
file-loader: 生成的文件名就是文件内容的 MD5 哈希值并会保留所引用资源的原始扩展名
url-loader: 功能类似 file-loader,但是文件大小低于指定的限制时，可以返回一个 DataURL 
事实上，在使用less,scss,stylus 这些的时候，npm 会提示你差什么插件，差什么，你就安上就行了
```

