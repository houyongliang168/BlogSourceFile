---
title: GitHub提交图片的几个基本命令
---
> 思路
1. 进入提交的文件夹  git bash 采用口令处理
2. 拉取代码
3. 提交代码
4. 图片可以通过复制到 微信上 变为小图片

## 1. 拉取代码
```bash
$ 从无到有  直接 clone 代码   git clone https://github.com/houyongliang168/Images-Folder.git

$ 从有到有  git pull origin master //从Github上pull到本地源码库

```

## 2. 提交代码
```bash
1. 提交本地
$ git add .
$ git commit -m "first commit"

2. 提交远程    remote 远程  origin 源
$ git remote add origin git@xx.xx.xx.xx:repos/xxx/xxx/xxx.git
$ git push -u origin 分支名  一般为 master 即  git push -u origin master

```

##3. git push 的 -u 参数具体适合含义
```bash
$ git push origin
上面命令表示，将当前分支推送到origin主机的对应分支。

如果当前分支只有一个追踪分支，那么主机名都可以省略。
$ git push
如果当前分支与多个主机存在追踪关系，那么这个时候-u选项会指定一个默认主机，这样后面就可以不加任何参数使用git push。
$ git push -u origin master
上面的命令将本地的 master 分支推送到 origin 主机，同时制定 origin 为默认主机，后面就不加任何参数使用 git push。
不带任何参数的 git push,默认只推送当前分支，这叫做simple 方式。


git push -u和git branch --set-upstream-to指令之间的区别。
举个例子：我要把本地分支master与远程仓库origin里的分支gaga建立关联。（如果使用下列途径1的话，首先，你要切换到master分支上（git checkout master））两个途径：1. git push -u origin gaga  2. git branch --set-upstream-to=origin/gaga master这两种方式都可以达到目的。但是1方法更通用，因为你的远程库有可能并没有gaga分支，这种情况下你用2方法就不可行，连目标分支都不存在，怎么进行关联呢？所以可以总结一下：git push -u origin gaga 相当于 git push origin gaga + git branch --set-upstream-to=origin/gaga master

```

##4. 他人资源

```bash

    添加已有项目到github

    新建repository，可以在github网站上直接新建或者使用windows github工具。

    进入github repository 项目
	
    在github windows工具中使用git Bash打开项目，使用cd命令进入已有项目根目录下
	
    touch README.md //新建说明文件
    git init //在当前项目目录中生成本地git管理,并建立一个隐藏.git目录
    git add . //添加当前目录中的所有文件到索引
    git commit -m "first commit" //提交到本地源码库，并附加提交注释
    git remote add origin https://github.com/chape/test.git //添加到远程项目，别名为origin
    git push -u origin master //把本地源码库push到github 别名为origin的远程项目中，确认提交
    提交完成，查看repository。

	更新代码

    cd /d/TVCloud
    git add .
    git commit -m "update test" //检测文件改动并附加提交注释
    git push -u origin master //提交修改到项目主线

    github常用命令

    git push origin master //把本地源码库push到Github上
    git pull origin master //从Github上pull到本地源码库
    git config --list //查看配置信息
    git status //查看项目状态信息
    git branch //查看项目分支
    git checkout -b host//添加一个名为host的分支
    git checkout master //切换到主干
    git merge host //合并分支host到主干
    git branch -d host //删除分支host
```