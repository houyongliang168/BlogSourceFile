---
title: 通过GitHub和Hexo来搭建自己的个人博客
---
参考：
[ 通过GitHub和Hexo来搭建自己的个人博客](http://www.jowanxu.top/2017/09/02/%E9%80%9A%E8%BF%87GitHub%E5%92%8CHexo%E6%9D%A5%E6%90%AD%E5%BB%BA%E8%87%AA%E5%B7%B1%E7%9A%84%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/#more)
[Hexo 使用中遇到的问题总结](http://blog.csdn.net/wx_962464/article/details/44786929)
[利用Hexo在多台电脑上提交和更新github pages博客](https://www.jianshu.com/p/0b1fccce74e0)
[github+hexo搭建自己的博客【真正的从0到1】20180122为准](http://blog.csdn.net/xiaoyu19910321/article/details/79134679)
[Hexo搭建教程](http://www.icafebolger.com/tags/hexo)
[Hexo搭建Github静态博客](http://www.cnblogs.com/zhcncn/p/4097881.html)
资源：
[淘宝 NPM 镜像](https://npm.taobao.org/)

## 注意事项

### 1.部署的时候执行：hexo deploy 命令行没有任何输出，也没有错误


``` bash
解决办法： 
在部署的_config.yml文件中，找到deploy:标签，在每个冒号后面必须要空格，否则就会出现上述问题。我的配置如下：


deploy:
type: git
repository: https://github.com/wx962464/wx962464.github.io.git
branch: master
```



### 2.部署提示找不到git 

``` bash
解决办法： 
在Hexo 3.0版本后deploy git 被分开的，所以需要安装，安装命令如下:npm install hexo-deployer-git --save ,安装好后在尝试一下就ok。
```



### 3. 执行 npm install -g hexo-cli 一直在等待，没有反应

``` bash
$ 是npm官方镜像连不通的问题. 尝试用淘宝的npm镜像后可以。执行 npm config set registry "https://registry.npm.taobao.org" 将npm包源指向淘宝。

$ npm install -g cnpm --registry=https://registry.npm.taobao.org
```


###4. 绑定域名

``` bash
$ 域名绑定 当前只对其 的主机记录和 记录值 进行了一次处理，发现可行
```

![Image text](https://raw.githubusercontent.com/houyongliang168/Images-Folder/master/img_2018_0223_github%E5%85%B3%E8%81%94%E7%BD%91%E7%AB%99.jpg)

###6. hexo d 报的错误，找不到 github .

``` bash
FATAL Something's wrong. Maybe you can find the solution here: http://hexo.io/docs/troubleshooting.html
Error: fatal: HttpRequestException encountered.
           ʱ    
bash: /dev/tty: No such device or address
error: failed to execute prompt script (exit code 1)
fatal: could not read Username for 'https://github.com': No error

    at ChildProcess.<anonymous> (D:\hexo\node_modules\hexo-util\lib\spawn.js:37:17)
    at emitTwo (events.js:126:13)
    at ChildProcess.emit (events.js:214:7)
    at ChildProcess.cp.emit (D:\hexo\node_modules\cross-spawn\lib\enoent.js:40:29)
    at maybeClose (internal/child_process.js:925:16)
    at Process.ChildProcess._handle.onexit (internal/child_process.js:209:5)

```
通过改变 
```bash
根目录下的 _config.yml 
将 repo: https://github.com/houyongliang.../blog.git 改为 repo: git@github.com:houyongliang.../blog.git


```


