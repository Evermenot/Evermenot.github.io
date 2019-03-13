---
title: Hexo自定义博客搭建
date: 2019-02-19 11:04:28
tags: [Blog, 折腾]
categories: [技术, Blog]
---

### 前言

​    一直以来都希望搭建一个简约而不简单的个人装X博客（本质目的还是为了写写心得哒~），最近因为时间宽裕的原因（其实是之前太懒233~），所以着手开始搭建，并记录下折腾的过程，以便之后回顾。

### 准备

- 下载安装Node.js （作用：生成静态页面；默认安装npm）

  - 注：hexo使用node开发，使用node进行markdown文件解析，生成html文件.

- 下载安装Git（作用：提交本地的hexo到github上去）

- 下载安装Hexo （npm install -g hexo）

  注：npm安装依赖包是从国外服务器上下载，受网络影响较大（容易‘便秘’），建议自己再安装个cnpm来替代npm（cnpm服务器在国内），命令：`npm install -g cnpm --registry=https://registry.npm.taobao.org`


### 本地搭建

- 在本地磁盘新建文件夹，如：blog--hexo
- 进入该文件夹内，打开cmd或运行git，输入并执行：hexo init（生成模板）
- npm install
- 运行hexo server 或 hexo s
- 测试：http://localhost:4000

### 关联Github

- 注册GitHub账号并在GitHub上创建xxx.github.io的项目，xxx为GitHub用户

- 打开本地_config.yml文件建立关联命令

  ```javascript
  deploy:
    type: git
    repository: https://github.com/Evermenot/Evermenot.github.io.git
    branch: master
  注：注意缩进，记得搭建过程中就是因为这个缩进有问题，一直不成功
  ```

- 安装部署模块 npm install --save hexo-deployer-git

- 生成本地静态文件：hexo g 或 hexo generate

- 推送到GitHub上：hexo d 或 hexo deploy

- 可能会用到的命令：hexo clean（清除缓存文件和已生成的静态文件）

- 测试：http://Evermenot.github.io

### 绑定域名

- 申请域名（如：阿里旗下的万网，以下也以万网为例）


- 在Evermenot.github.io仓库项目设置Settings中,找到Custom domain将申请的域名填写上去保存
- 域名解析（如：阿里云）：
  - 添加记录： 如添加一条A记录：
    - 记录类型：A
    - 主机记录：@
    - 解析路线：默认
    - 记录值：192.30.252.153/154（该IP地址是由GitHub提供的，两个可以都配置）
    - 注：你也可以在cmd中将Ping Evermenot.github.io得到的IP（185.199.108.153）填进去，好像185.199.108.153 是 192.30.252.153下的子IP
  - 添加CNAME记录：主机记录为www（防止3w形式无法访问www.lecode.ltd），记录值为Evermenot.github.io，其他和A记录一样
  - 注：A记录就是把一个域名解析到一个**IP地址**（Address），而CNAME记录就是把域名解析到另外一个**域名**。
- 添加CNAME文件（文件内容为 你申请的域名如：lecode.ltd）
  - 方法一：在github仓库项目中添加CNAME文件（无后缀名且名称大写）
  - 方法二：进入本地博客，在source目录下添加CNAME文件（防止推送文章时，将仓库中的CNAME覆盖掉）
- 注域名前最好不要加www，因为加上www的为二级域名

如无意外，到此博客初步搭建完成

下面就是自定义的内容了...生命不息，折腾不止~.~（思想觉悟还是要有的）

### 主题

选择一款你喜欢的[Hexo主题](https://hexo.io/themes/)，推荐使用[NexT](https://github.com/theme-next/hexo-theme-next)（NexT因其简约强大的特性，使用者很多，网上很多相关教程也多以NexT为列）

#### 下载及使用

- 下载主题
  - 方式一：
      cd到themes文件夹下
      git clone https://github.com/theme-next/hexo-theme-next
  - 方式二：
      直接下载zip包，然后解压到themes目录下


- 使用主题

  - 方式一：

      在博客根目录，使用 vi _config.yml 打开配置文件，在输入i/insert 进入编辑模式，将theme键对应的值改为 下载好的主题文件夹名字，如：hexo-theme-next（名称随便改，切勿太死板）

  - 方式二：

      直接使用编辑器打开_config.yml文件修改，这种方式逼格太low~

  - 在主题的配置文件_config.yml中scheme设置主题样式

#### 自定义菜单

- 添加页面

  - 新建页面：hexo new page categories或tags

    作用：在source文件夹下创建categories文件夹，该文件夹有一个下的index.md文件（本过程亦可手动创建）

  - 设置页面类型（index.md文件中，以分类为例）

    ```
    ---
    title: categories
    date: 2019-01-31 15:41:26
    type: "categories"
    ---
    ```

  - 修改配置

    ```
    // 类似于配置路由
    menu:
      home: /
      archives: /archives/
      categories: /categories/
      tags: /tags/
      about: /about/
    ```

  - 文章层面：

    ```
    ---
    title: Hexo自定义博客搭建
    date: 2019-02-19 11:04:28
    tags: Blog
    categories: 技术
    ---
    # 多个标签支持 tags: [Blog, 折腾]形式（分类同理哈）
    # 分类和标签上面写法在渲染时，存在不同，[分类a, 分类a的子分类...]
    ```

- 添加自定义页面

  1. 创建和分类页类似的文件夹及内部文件

  2. 编辑主题配置文件_config.yml中的menu，如：

     ```
     menu:
       home: /
       categories: /categories/
       book: /book/
     # book: /categories/书摘/
     # 以上面的形式设置菜单项（书摘--不需要再创建文件夹）的跳转也是可以的
     ```

  3. 编辑`主题目录/languages/zh-Hans.yml` 添加中文翻译

     ```
     menu:
       home: 首页
       categories: 分类
       book: 书摘
     ```

  4. 设置图标，默认显示问号（主题配置文件）

     ```
     menu_icons:
       enable: true
       home: home
       about: user
     # http://www.fontawesome.com.cn/faicons/
     ```

  5. 添加实验室（目的：折腾）

     ```
     # 重复上面的4步
     # 实验室文件夹下的内部文件换为index.html
     # 编辑主配置文件
     skip_render: /lab/

     # lab文件夹下的内容将不会转化为html，会直接被copy到public文件夹
     # 做完上面的步骤，你就可以愉快的在实验室瞎折腾了~
     ```


#### 文章加密

- 背景：在写写技术博客的时候，偶尔写写心情，不愿意给所有人都看到，这个时候就需要对发布的文章进行加密了

- 加密方式一：

  ```
  //1. 打开themes->next->layout->_partials->head.swig文件，在meta标签后面插入下面代码
  (function(){
     if('{{ page.password }}'){
         if (prompt('请输入文章密码') !== '{{ page.password }}'){
            alert('密码错误！');
            // history.back(); （微信上以链接的形式直接进入文章页面会无法后退，加密功能将会不起作用，输入完错误密码后还是会显示文章哦）
            // 简单粗暴的方法：
            location.href = 'http://lecode.ltd'
         }
     }
  })();
   //2. 文章层面
   // 文章信息存放在page对象里（page.password）
    title: 你知不知道什么是当当...当当当？
    tags: 当当当
    password: mima
  ```

- 加密方式二：

  ```
  // 1. 安装插件 npm install --save hexo-blog-encrypt
  // 2. 主配置文件_config.yml中，启用插件
    encrypt:
      enable: true
  // 3. 文章层面
    title: 加密
    password: mima
    abstract: Welcome to my blog, enter password to read.
    message: Welcome to my blog, enter password to read.
    // password: 是该博客加密使用的密码
    // abstract: 是该博客的摘要，会显示在博客的列表页
    // message: 这个是博客查看时，密码输入框上面的描述性文字
    // 注：使用的时候有个问题：图片的点击放大预览功能会失效
    // 文档：https://github.com/MikeCoder/hexo-blog-encrypt/blob/master/ReadMe.zh.md
  ```


#### 添加看板娘

- 安装插件 npm install --save hexo-helper-live2d

- 在主配置文件或 主题配置添加配置信息

  ```
  live2d:
  enable: true
  model:
    use: wanko #nico #haru模板名
    scale: 1
    hHeadPos: 0.5
    vHeadPos: 0.618
  display:
    superSample: 2
    width: 75
    height: 150
    position: right
    hOffset: 0
    vOffset: -20
  mobile:
    show: true
    scale: 0.5
  react:
    opacityDefault: 0.7
    opacityOnHover: 0.2
  ```

- 模型

  ```
  //1. 在博客根目录下创建一个 live2d_models 文件夹
  //2. 将你的 Live2D 模型复制到这个子文件夹中
  //3. 将模型文件夹的名称输入 _config.yml 的 model.use 中
  //模型：https://github.com/xiazeyu/live2d-widget-models
  //npm install 模型的包名 来安装
  //node_modules会下载到该文件夹下，可以自己拷贝出来
  ```

#### 添加网易云音乐歌单

- 打开网易云音乐（网页版），点击选择某一歌曲，将地址栏中的id参数值复制下来

  ```
   // 将下面代码插入到你希望的位置，记得将下面id的值换成你想要的歌曲id
  <iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=1371207&auto=0&height=66"></iframe>
  ```
### 参考文章

- http://www.monster1935.com/2017/04/20/hexo%E6%B7%BB%E5%8A%A0%E8%87%AA%E5%AE%9A%E4%B9%89%E9%A1%B5%E9%9D%A2/

- https://thief.one/2017/03/03/Hexo%E6%90%AD%E5%BB%BA%E5%8D%9A%E5%AE%A2%E6%95%99%E7%A8%8B/

  感谢大佬们的知识分享

-----------------大致先更新到这里吧，博客优化感觉是个无底洞......