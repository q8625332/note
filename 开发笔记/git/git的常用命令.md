## git的常用命令
![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZXMyMDE1LmNuYmxvZ3MuY29tL2Jsb2cvMTAwOTY4Ni8yMDE2MDgvMTAwOTY4Ni0yMDE2MDgyNDEwMDEyNzg3MC0xODIwNzg2ODM2LnBuZw)
## git生成ssh

```java
ssh-keygen -t rsa -C "xxxxx@xxxxx.com邮箱账号"
```

## 参考文档
[初学者，可以鉴赏。分析详细](https://www.liaoxuefeng.com/wiki/896043488029600/896067008724000)
## Git的配置

```
git config --global user.name "yourname"

git config --global user.email "your_email@youremail.com"

git config --list //然后查看自己的配置
```
## 连接远程仓库

```
git remote add origin git@github.com:yourName/repositoryname.git

git remote add origin https://github.com/yourName/repositoryname.git
```
## 创建分支
**首先，我们创建dev分支，然后切换到dev分支：**
```java
$ git checkout -b dev
Switched to a new branch 'dev'
```
**git checkout命令加上-b参数表示创建并切换，相当于以下两条命令：**

```java
$ git branch dev
$ git checkout dev
Switched to branch 'dev'
```
**然后，用git branch命令查看当前分支：**

```java
$ git branch
* dev
  master
```
**git branch命令会列出所有分支，当前分支前面会标一个*号。**
**合并分支**

```java
//先切换回主分支
$ git checkout master
Switched to branch 'master'
//在合并dev分支
$ git merge dev
Updating d46f35e..b17d20e
Fast-forward
 readme.txt | 1 +
 1 file changed, 1 insertion(+)
```
**删除分支**

```java
$ git branch -d dev
Deleted branch dev (was b17d20e).
```
## 多人协作

> 当你从远程仓库克隆时，实际上Git自动把本地的master分支和远程的master分支对应起来了，并且，远程仓库的默认名称是origin。要查看远程库的信息，用git remote：

```java
$ git remote
origin
```

> 或者，用git remote -v显示更详细的信息：

```java
$ git remote -v
origin  git@github.com:michaelliao/learngit.git (fetch)
origin  git@github.com:michaelliao/learngit.git (push)
```

## 推送分支

> 推送分支，就是把该分支上的所有本地提交推送到远程库。推送时，要指定本地分支，这样，Git就会把该分支推送到远程库对应的远程分支上：

```java
$ git push origin master
```

> 如果要推送其他分支，比如dev，就改成：

```java
$ git push origin dev
```
