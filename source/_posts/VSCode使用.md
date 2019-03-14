---
title: VSCode使用
date: 2018-03-28 17:34:33
tags: vscode
categories: [技术, 工具]
description: VSCode使用
---

### 一.常用快捷键

```javascript
/*
1. 格式化代码                     Alt + Shift + F
2. 打开或关闭资源管理器            Ctrl + B
3. 打开浏览器                     Ctrl + F1
4. 代码折叠(组合键)                Ctrl + K   Ctrl + 0 
5. 多选                           Alt + 左键
6. 查找                           Ctrl + F
7. 替换                           Ctrl + H
```

### 二.常用自定义设置

```json
// changeStart
"editor.fontSize": 18,
// "editor.fontFamily": "'Courier New'",
"editor.renderWhitespace": "all",
"editor.rulers": [
    80,
    120
],
"editor.cursorBlinking": "expand",
"workbench.iconTheme": "vscode-icons",
"editor.wordWrap": "off",
"files.exclude": {
    "**/.git": true,
    "**/.svn": true,
    "**/.hg": true,
    "**/CVS": true,
    "**/.DS_Store": true,
    "**/node_modules": true
},
// changeEnd
```



### 三.常用插件部分

```javascript
/*
1. Auto Rename Tag           自动化重命名标签功能
2. Path Intellisense         路径提示功能
3. View In Browser           在浏览器中打开页面功能
4. vscode-icons              加上图标区分对应文件功能
5. Live Server               以服务的形式打开文件功能
6. easy less				Generates a .css file each time you save a .less file.
```

### 四.使用VSCode 编译Sass

```javascript
/*
1. 安装Sass的编译依赖环境Ruby     (Ruby只提供运行环境,不了解也没关系)
	注：安装Ruby时要勾选 Add Ruby executables to your PATHRuby(目的是将Ruby添加到系统变量)官网下载链接https://rubyinstaller.org/downloads/  
	
2. Rudy安装完成，在命令行输入'gem sass'来安装sass
    
3. 在VSCode中安装sass插件 'easysass'

4. 添加配置(VSCode用户配置)
    "easysass.compileAfterSave": true,
    "easysass.excludeRegex": "",
    // 定义scss文件生产的css文件格式.
    "easysass.formats": [
        {
            "format": "expanded",
            "extension": ".css"
        }
    // 输出的目录 默认和源文件相同
    "easysass.targetDir": ""
       
 注：我没有进行这一步(我用npm全局安装了Sass，或许是Ruby中带的？？？)  还有还有我在想如果不装Ruby直接npm全局安装Sass，然后在vscode里安装'easysass'插件再配置sass,最后生成.css文件是不是也可以？？？
```



### 五.Vue 代码段

```javascript
{
    "Print to console": {
        "prefix": "vue",
        "body": [
            "<template>",
            "    <div>\n",
            "    </div>",
            "</template>\n",
            "<script>",
            "export default {",
            "    components: {\n",
            "    },",
            "    data () {",
            "        return {\n",
            "        }",
            "    },",
            "    mounted() {\n",
            "    },",
            "    methods: {\n",
            "    }",
            "}",
            "</script>\n",
            "<style scoped lang='less'>\n",
            "</style>",
            "$2"
        ],
        "description": "Log output to console"
    }
}
```

