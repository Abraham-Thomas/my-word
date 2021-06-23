git作为一款如今每个程序员都要用的工具，自然是学会如何使用。
这里依然只介绍工作中常用的一些操作，详细的请移步官网：[git文档](https://git-scm.com/doc)

Git 的工作就是创建和保存你项目的快照及与之后的快照进行对比。

我们常用以下 6 个命令：git clone、git push、git add 、git commit、git checkout、git pull

1、傻瓜式安装你的git到PC上，比如如windows。
2、添加配置：

```
git config --global user.name "你的名字"
git config --global user.email "你的Email"; 
git config --list   查看config的配置值;

```
3、在指定文件夹下初始化工作仓库

```
git  init
```
4、将服务端项目clone到本地
```
git  clone   xxxxx.git
```

5、下面介绍git 的常用命令：

`git   status` 查看本地仓库当前的状态（被修改过的文件、新增加或删除的文件等);  

`git   add  xxx.txt`   将文件添加到本地仓库中; git  add * 将所有文件都添加到本地仓库中;

`git   commit   -m  "commit log"` 提交修改的文件;

`git push   -u   origin master` 提交到远程服务器上，` -u origin master`第一次提交后，后面提交可以省略了。

` git checkout [<branch>] ` 更新工作树中的文件以匹配索引或指定树中的版本。如果没有给出路径规范，git checkout也会更新HEAD以将指定的分支设置为当前分支。

`git   log` 查看提交的历史记录

`git   pull   branchname` 拉取指定分支的最新代码

`git   branch` 查看本地分支， 后面加上 `-a `  查看远程分支

`git   diff xx.txt` 查看具体修改的内容，其实现在用的不是很多，vscode里面安装插件其实可以看到同样的效果。

