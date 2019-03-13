---
title: Windows系统下的Apache配置并支持PHP
date: 2018-03-08 16:54:09
tags: [WAMP, Apache, PHP]
categories: [技术]
---

### 一.Apache服务器软件安装即配置

> - 第一步：创建文件夹WAMP(Windows、Apache、MySQL、PHP缩写)
>
> - 第二步：安装Apache的运行环境VC++、而且php也需要C++的运行环境
>
> - 第三步：将解压好的Apache免安装文件拷贝到WAMP文件夹下   (我的是免安装版64位的)
>
> - 第四步：找到Apache文件夹下的conf/http.conf文件
>
>   ```php
>   // 修改http.conf文件
>   1. 找到Define SRVROOT  修改为：Define SRVROOT "apache的安装路径"
>   2. 找到DocumentRoot    修改为：DocumentRoot "网站根目录的路径"（如：PHP文件）
>   3. 修改<Directory>     修改为：<Directory "网站根目录的路径">
>   // 路径方面一定要使用全英文，apache不识别中文
>   ```
>
> - 第五步：找到Apache文件夹下的bin/httpd.exe双击运行 不黑屏闪退即安装成功  在浏览器中输入127.0.0.1验证       （netstat  -ano 查看端口使用情况）
>
> - 第六步：
>
>   ```javascript
>   // 找到<IfModule dir_module><IfModule>
>   /* 修改为： <IfModule dir_module>
>              		DirectoryIndex index.html index.php    
>              		// 默认加载页依次去找，前面的优先级最高，没有则启用目录浏览
>              <IfModule>*/
>   ```
>
> - 第七步：安装Apache服务
>
>   ```javascript
>   // 安装服务
>   1. httpd.exe -k install -n "Apache"   (-n 指定服务名称·非强制性)
>   // 卸载服务
>   2. httpd.exe -k uninstall -n "Apache"
>   *注：在没有配置前六步的情况下直接安装会报错，执行'httpd.exe -t'测试配置文件，显示Syntax OK即表示成功
>
>   // 启动服务
>   3. httpd.exe -k start -n "Apache"
>   // 重启服务
>   4. httpd.exe -k restart -n "Apache"或httpd -k shutdown -n "Apache"
>   // 停止服务
>   5.3. httpd.exe -k stop -n "Apache"
>
>   ```
>
>   ​
>
>

### 二.PHP安装及配置

> - 第一步：将解压好的PHP免安装文件拷贝到WAMP文件下
>
> - 第二步：在Apache中添加PHP的配置
>
>   ```javascript
>   // 在Apache的httpd.conf文件的末尾 添加 如下几行命令
>   // 在Apache上挂载 php
>   1. LoadModule php5_module "D:/WAPM/PHP/php5apache2_4.dll"
>
>   // 将.php后缀名的文件交Apache处理
>   2. AddType application/x-httpd-php .php
>
>   // php.ini php的配置文件
>   3. PHPIniDir "D:/WAPM/PHP/php.ini"
>
>   // 不配置的话，php文件只能原封不动的显示到页面上，因为Apache只能处理静态文件请求，.php文件为动态文件
>   // 如果以上步骤还不能成功加载.php文件，试下再将PHP文件夹下的php.ini-development或php.ini-production 修改成php.ini文件
>   // 并修改php.ini文件 734行 
>   4. extension_dir = "ext"  修改成：extension_dir = "D:/WAMP/PHP/ext"
>
>   //注：指定php相应扩展的目录(apache默认去Windows目录下找php.ini)，如果这个不设置，只能使用php核心功能，另外的curl操作，mbstring字符串操作，xml操作，mysql操作都不能进行，让php通过php.ini配置文件读取ext扩展目录
>   ```
>   ​
>
> -  第三步：其他配置
>
>   ```javascript
>   // 开启扩展  php.ini   890行
>   1. 添加字符串扩展mbstring，mysql及mysqli扩展等 
>   // 修改时区  [Date]    935行
>   2. date.timezone = Asia/Shanghai或者date.timezone = PRC
>   // 在程序里面用date_default_timezone_set()函数设置相同，默认是UTC格林威治时间，和北京时间相差8个小时
>
>   // 开发阶段错误提示扩展
>   3. display_errors = On    (开发设置为On，上线设置为Off)
>   ```
>
>   相关参考文章https://www.cnblogs.com/freeweb/p/5056979.html
>
>   ​

### 三. MySQL安装

> - 第一步：将解压好的MySQL免安装文件拷贝到WAMP文件下
>
> - 第二步：安装服务
>
>   ```javascript
>   // 命令行切换到bin目录下
>   // 初始化数据所需要的文件以及获取一个临时访问密码
>   1. mysqld --initialize --user=mysql --console   
>   // 生成一个data文件夹 临时密码记得拷贝下来  不然还得重来
>
>   // 安装为服务
>   2. mysqld --install MySQL   （非强制名称）
>   // 进入MySQL (使用刚刚备份的临时密码登录)
>   3. mysql -u root -p
>   // 设置数据库密码
>   4. set password for root@localhost = password('root');
>
>   // 退出验证密码
>   5. exit 重复步骤3
>
>   注：数据库操作命令记得加上分号啊啊啊';'
>   ```
>

### 四.配置虚拟主机

> - 第一步：开启虚拟主机配置
>
>   ```javascript
>   // 配置虚拟主机的目的：实现一台服务器可以提供多站点服务（即部署多个网站），实质就是访问服务器上的不同目录，配置apache虚拟主机的方式有：基于域名的配置、基于端口和IP的配置方法
>
>   // 找到httpd.conf文件的494行 Virtual hosts   实质：载入虚拟主机配置文件文件
>   1. 开启虚拟主机将'Include conf/extra/httpd-vhosts.conf'前的#号去掉
>   ```
>
>   ​
>
> - 第二步：添加配置选项
>
>   ```javascript
>   // 找到conf/extra/httpd-vhosts.conf
>   2. 在配置文件的末尾添加如下配置：
>   # 配置虚拟主机
>   <VirtualHost *:80>
>      # 网站管理员邮箱   可以随意写（可删）
>      ServerAdmin webmaster@dummy-host2.example.com
>      # 虚拟主机的根目录 即站点的文件（代码目录）
>      DocumentRoot "C:/Users/v_enlli/Desktop/website01"
>      # 虚拟主机的域名
>      ServerName web01.com
>      # 虚拟主机的别名alias *代表'.'前面部分通配
>      ServerAlias web01.com *.web01.com
>      # 虚拟主机的错误日志  一般在apache的logs目录下（可删）
>      ErrorLog "logs/dummy-host2.example.com-error.log"
>      # 虚拟主机访问日志(可删)
>      CustomLog "logs/dummy-host2.example.com-access.log" common
>
>      # 默认加载页（非必须）
>      DirectoryIndex index.html
>      # 文件访问权限设置
>      <Directory "C:/Users/v_enlli/Desktop/website01">
>           # FollowSymLinks: 在该目录下允许文件系统使用符号连接
>           # Indexes: 当用户访问该目录时，如果用户找不到DirectoryIndex指定的主页文件(例如index.html),则返回该目录下的文件列表给用户
>           Options Indexes FollowSymLinks
>           # AllowOverride All 表示允许使用.htaccess文件重写URL
>           AllowOverride None
>           Order allow,deny
>           Allow from all
>           Require all granted
>      </Directory>
>   </VirtualHost>
>
>   ```
>
> - 其他
>
>   ```javascript
>   // 报错：Invalid command 'Order', perhaps misspelled or defined by a module not included in the server config
>   // 解决：将LoadModule access_compat_module modules/mod_access_compat.so前#号去掉
>   // 403 Forbidden  （原因有很多 我的是添加了下面的命令）
>   // 解决：添加 Require all granted   允许所有的请求
>   // 注：记得将httpd.conf文件中的关于之前主机的配置项给注释掉哦哦哦
>   参考文章：http://blog.csdn.net/stefshawn/article/details/6826194
>            http://www.cnblogs.com/yeer/archive/2011/01/18/1938024.html
>   ```
>
>   ​
>
>

