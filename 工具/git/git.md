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

## gitlab删除已上传文件或文件夹

将仓库拉取到本地

```
git clone .....
```

在本地仓库删除文件

```
git rm 文件名
```

在本地仓库删除文件夹

```
git rm -r 文件夹/

//-r表示递归所有子目录，如果你要删除的是空文件夹，可以不用带-r
```

提交代码

```
git commit -m "提交描述"
```

推送到远程仓库

```
git push origin XXXXXX
```

```
//删除idea
git rm --cached -r .idea
git commit -m 'delete .idea'
git push origin master

```

### 提交

```
git add .

git commit -m '更新信息'

git push origin master
```

### .gitignore失效

```
git rm -r --cached .
```



