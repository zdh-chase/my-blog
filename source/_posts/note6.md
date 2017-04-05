---
title: git日常提交步骤-基本命令行
tags:
     - git
---
## 1.下载代码

``` bash
git clone http//xxxx.git –b dev xxxx_dev_local
```
<!--more-->
注解：
1)   -b dev   指定想要下载的分支，去掉省略的话下载的是默认分支是master（注：默认分支可以修改）
2)   xxxx_dev_local是我想要在本地创建的git库文件夹名字，去掉省略的话下载好的git库名字是xxxx git库的名字，建议使用这种重命名方式

## 2.更新代码

注：每次修改前养成同步更新代码的好习惯
``` bash
git pull –rebase
```
如果提示冲突，说明本地git库中未入库的提交中有修改和代码库中冲突了，修改冲突文件并删除冲突标识<<<<< ==== >>>>后:
``` bash
git add 冲突文件名               //加入git index中
git rebase –continue                 //继续更新到最新的base上
```


## 3.修改、增加或删除代码文件到git index缓存中

修改或新增加代码文件：
``` bash
git add file_name
```
删除代码文件：
``` bash
git rm file_name
```
## 4.提交本地git库index缓存中的修改
``` bash
git commit –m “just a test for commit ”
```
如果本地修改不对或commit的LOG需要修改可以使用：
``` bash
git commit –-amend //修复上一次提交，不要修改Merge提交
```
##5.更新代码
注：每次push前养成同步更新代码的好习惯
``` bash
git pull –rebase
```
如果提示冲突，说明本地git库中未入库的提交中有修改和代码库中冲突了，修改冲突文件并删除冲突标识<<<<< ==== >>>>后:
``` bash
git add 冲突文件名               //加入git index中
git rebase –continue                 //继续更新到最新的base上
```
##6.推送提交到服务器上的git库中
``` bash
git push
```
注：默认会推送到我们下载git库时的-b 后面加的那个分支，没有加默认是master，这个命令完整版实际上是:
``` bash
git push origin 当前分支名:我们下载的分支名
```
##7.解消代码冲突
``` bash
git merge xxxx_branch 或者 git pull 时 or
git rebase xxxx_branch 或者 git pull --rebase 时
```
解消方法也是修改冲突文件并删除冲突标识<<<<< ==== >>>：
``` bash
git rebase –continue                 //继续更新到最新的base上
```

over