## Git 出现SSL certificate problem: self signed certificate的解决方法

```
git config --global http.sslVerify false
```

## 简单的代码提交流程

```
git status //查看工作区代码相对于暂存区的差别
git add . //将当前目录下修改的所有代码从工作区添加到暂存区 . 代表当前目录
git commit -m ‘注释’ //将缓存区内容添加到本地仓库
git push origin master //将本地版本库推送到远程服务器， origin是远程主机，master表示是远程服务器上的master分支，分支名是可以修改的
```

https://blog.csdn.net/qq_37577660/article/details/78565899

