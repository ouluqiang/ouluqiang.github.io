---
title: 'Gridea保存源文件'
date: 2019-09-24 15:55:55
tags: []
published: true
hideInList: false
feature: 
---
## 创建分支
在GitHub的博客项目中新建一个分支

![](https://ouluqiang.github.io//post-images/1569311927040.jpg)

克隆项目到本地  
```
git clone https://github.com/xxx/xxx.github.io.git
```

## 切换分支

到本地项目中打开Git Bash, 切换到项目分支，查看分支，用 git checkout XXX 切换分支
```
# 列出所有本地分支
$ git branch

# 列出所有远程分支
$ git branch -r

# 列出所有本地分支和远程分支
$ git branch -a

# 切换到指定分支，并更新工作区
$ git checkout [branch-name]
```
![](https://ouluqiang.github.io//post-images/1569311864422.jpg)

## 生成ssh-key的私钥和公钥
生成key,看C盘用户目录SSH

```
ssh-keygen -t rsa      //一路回车下来
```
![](https://ouluqiang.github.io//post-images/1569314857492.jpg)

测试是否能连接上GitHub服务器
```
ssh -T git@github.com
```
输出是.........
Permission denied (publickey).
需要将上面生成的public key(id_rsa.pub文件内容)拷贝到github服务器的SSH Keys中，
再.ssh目录下打开终端输入下面代码查看内容，或者直接记事本打开查看

```
cat id_rsa.pub
```

![](https://ouluqiang.github.io//post-images/1569315446419.jpg)

头像 > settings > ssh
![](https://ouluqiang.github.io//post-images/1569315521381.jpg)

再测试下是否连接GitHub服务器 ssh -T git@github.com 能显示GitHub用户名就表示连接成功
![](https://ouluqiang.github.io//post-images/1569315793106.jpg)



把源文件复制到分支项目,再打开Git Bash 提交代码
![](https://ouluqiang.github.io//post-images/1569311872576.jpg)


```
# 添加当前目录的所有文件到暂存区
$ git add .

# 提交暂存区到仓库区
$ git commit -m ‘修改说明，随便写’

# 提交到远程仓库
git push
```

提交成功
![](https://ouluqiang.github.io//post-images/1569311915447.jpg)

有一个问题就是提交完会导致token失效