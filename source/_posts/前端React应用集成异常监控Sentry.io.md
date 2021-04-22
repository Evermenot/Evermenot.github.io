---
title: 前端React应用集成异常监控Sentry.io
date: 2020-07-24 21:04:09
tags: [Blog]
categories: [Blog, 技术]
---

#### 安装依赖

```javascript
// Sentry SDK
yarn add @sentry/react @sentry/apm
```

#### 初始化

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import * as Sentry from '@sentry/react';
import { Integrations as ApmIntegrations } from '@sentry/apm';
Sentry.init({
  dsn: "https://c25359fc81bc453cafc13f@o41158.ingest.sentry.io/531806",
  release: 'xhqb-web@0.1.0',
  integrations: [
    new ApmIntegrations.Tracing(),
  ],
});
ReactDOM.render(<App />, document.getElementById('root'));
```

##### dsn字段组成

- 组成{PROTOCOL}://{PUBLIC_KEY}@{HOST}/{PROJECT_ID}

  ```javascript
  1. 使用的协议；http或https
  2. 验证sdk的公钥；c25359fc81bc453cafc13f
  3. 目标sentry服务器；o41158.ingest.sentry.io
  4. 验证用户绑定的项目id；531806
  ```

- dsn入口

![1](/images/sentry/1.png)

![2](/images/sentry/2.png)


#### 配置Source Maps

> 作用：定位报错文件&位置（未压缩前）
>
> 注：Source Maps文件上传成功后，只能定位到线上环境的报错文件（本地localhost是不生效的）

##### 安装依赖

```javascript
yarn add --dev @sentry/webpack-plugin
yarn global add @sentry/cli
```

##### webpack文件配置

###### 项目打包（npm run build）时自动上传SourceMap 至 Sentry

```javascript
const SentryWebpackPlugin = require('@sentry/webpack-plugin');
plugins: [
  new SentryWebpackPlugin({
    release: 'xhqb-web@0.1.0',    // 发布的版本名称（每个版本都会生成属于该版本的监控记录）
    include: './build/static/js', // 要上传的文件目录
    urlPrefix: '~/static/js',     // 线上js资源的路径，~相当于test-crm.xhqb.io
    ignore: ['node_modules', 'webpack.config.js'],
    configFile: '.sentryclirc',   // 内容较多，下面介绍
  }),
]
```

##### 配置.sentryclirc文件

> 1. 在项目根目录下创建.sentryclirc文件
>
> 2. 在项目根目录下运行`sentry-cli login`命令进行授权 
>
>    注：没有token，可以自己创建一个
>
> 3. 配置defaults（见下文）注：必须将 org 和 project 补充上去

```javascript
[auth]
token=c9b46863faa54049bedccae10f4d63ba614be95e9e192a

[defaults]
url=https://sentry.io/
org=6b05882f9f
project=crm // sentry.io中的项目名
```

###### 创建/拷贝token

![3](/images/sentry/3.png)

![4](/images/sentry/4.png)

###### org字段

![5](/images/sentry/5.png)

##### 查看Source Maps是否上传成功

> 入口：Settings>Projects>crm>Source Maps (里面有对应版本的Source Maps)
>
> [crm的SourceMaps](https://sentry.io/settings/6b0554882f9f/projects/crm/source-maps/)



##### sentry/cli常用命令

```javascript
// 清除xhqb-web@0.1.0版本的sourceMap
sentry-cli releases files xhqb-web@0.1.0 delete —all

// 手动上传xhqb-web@0.1.0版本的sourceMap
sentry-cli releases files xhqb-web@0.1.0 upload-sourcemaps --url-prefix '~/static/js' './build/static/js'

// 查看已发布的版本
sentry-cli releases list
```



#### 基本使用

##### 主动上传报错信息

```javascript
Sentry.captureException(new Error('error message')) 
```

##### 弹窗提交错误报告(不建议使用)

```javascript
Sentry.init({
  dsn: "https://c25359fc81bc696e9b9afc13f@o414158.ingest.sentry.io/53706"
  beforeSend(event, hint) {  
     // Check if it is an exception, and if so, show the report dialog  
     if (event.exception) {  
       Sentry.showReportDialog({ eventId: event.event_id });  
     }  
     return event;  
   }, 
});
```

