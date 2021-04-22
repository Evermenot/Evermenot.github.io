---
title: git使用
date: 2018-03-07 09:14:05
tags: git
categories: [技术, 工具]
---

[相关文章](http://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html)

#### 关联远程仓库

> `git remote add origin [远端仓库地址] ` 
>
> 如：git remote add origin  http://kb-webide

#### 创建分支

```javascript
// 创建本地分支
1. 'git branch -b [分支名]'    // 创建分支并停留在当前分支
2. 'git checkout -b [分支名]'  // 创建分支并切换到该分支，区别于上面的命令
3. 'git push origin [分支名]'  // 将本地分支提交到远程仓库   即在远程仓库创建分支
```

#### 查看分支

```javascript
// 列出分支名
1. git branch				 // 列出本地分支
2. git branch -r			 // 列出远程分支
3. git branch -a			 // 列出本地和远程分支
```

#### 切换分支

```javascript
// 切换到其他分支
1. git checkout [分支名]	   // 切换到指定分支
2. git checkout -			 // 切换到上个分支	
```

#### 恢复暂存区文件到工作区

> `git checkout .`   等于是撤销操作           

#### 删除分支

```javascript
1. git branch -d [分支名]		            // 删除本地分支
2. git push origin --delete [分支名]		// 删除远程分支
3. git branch -dr [remote/branch]
```
#### 删除github文件夹

```javascript
1. git rm -r --cached [文件夹名]            // --cached不会删除本地的对应文件
2. git commit -m "delete files"			   // 提交删除操作
3. git push origin [分支名]				 
```

#### 删除本地文件

```javascript
git rm 文件路径（src/test.vue）
// -f 强制删除
// -r 删除文件夹及文件
git checkout --src/test.vue 恢复被删除文件
```

#### 抽取提交合并到其他分支

```
git cherry-pick commitId
```



#### 版本回滚

```javascript
git log
git log --pretty=oneline (git log信息简洁版)
// 回滚
git reset --hard 版本号

//注：回滚后的版本提交到GitHub上会报错，git默认是高版本覆盖低版本，但不能反过来操作，因为回滚的版本比服务器此时的版本低，所以此时常规手段是无效的，但可以进行版本的暴力提交——force
git push -f
```

#### git同步代码到Coding和Github

- 修改.git文件夹下的config文件

  ```
  [core]
  	repositoryformatversion = 0
  	filemode = false
  	bare = false
  	logallrefupdates = true
  	symlinks = false
  	ignorecase = true
  [remote "origin"]
  	url = git@github.com:Evermenot/Evermenot.github.io.git
  	fetch = +refs/heads/*:refs/remotes/origin/*
  [remote "second"]
  	url = git@git.dev.tencent.com:Evermenot/Evermenot.git
  	fetch = +refs/heads/*:refs/remotes/second/*
  ```


- 代码push命令

  ```
  git push second hexo
  ```

  ​

