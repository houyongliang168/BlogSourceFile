---
title: 变更git 远程的账号和密码
---

## 1. 使用情形：
1. 今天帮同事在 安装 git 软件，感觉重新复习了下流程，在安装完成之后，使用我的 远程账号和密码 使用了一次，拉取代码；然后就发现 其 git 一直使用 我的 账号和密码，为了避免在 使用过程中 他提交出现问题后 导致的责任问题，强烈的感觉 要 换个 远程的 账号和密码。
2. 类似的情形也比较多，比如说：
      2.1 更换 git  远程的账号和密码
      2.2 多个 远程的 git 账号和密码 切换使用。

## 2. 理思路：
1. 远程git  的账号和密码 开始会缓存在本地， 通过使用git config --global credential.helper store指令可以保存到本地。保存本地的一个配置文件里面，资料显示 文件在 .git-credentials ,默认安装 会在C:\Users\Administrator\.git-credentials 里面。
2. 进入 .git-credentials 文件，对其进行修改。删除或修改 样式为 http://账号:密码@git仓库http地址 。

## 3. 别人的解决办法
```
linux下

在~/下， touch创建文件 .git-credentials, 用vim编辑此文件，输入内容格式：

touch .git-credentials

vim .git-credentials

https://{username}:{password}@github.com


2. 在终端下执行  git config --global credential.helper store

3. 可以看到~/.gitconfig文件，会多了一项：


[credential]

    helper = store


```



