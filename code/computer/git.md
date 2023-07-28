---
title: Git
icon: git
date: 2022-11-09
category:
  - 计算机基础
tag:
  - git
---

git作为目前最流行的代码版本控制管理工具。

学习网站参考：

- **git官网：** <https://git-scm.com/book/zh/v2>

- **菜鸟教程** <https://www.runoob.com/git/git-tutorial.html>

## git基本命令

git使用前 配置提交人信息

```bash
git config --global user.name 姓名       配置姓名
git config --global user.email 邮箱      配置邮箱
```

常用命令

```bash
git init                      初始化git仓库
git status                    查看文件状态

git add 文件名                	将文件添加到暂存区
git add .                     将全部文件添加到暂存区
git commit -m 提交信息     		将暂存区中的文件提交到本地库

git reflog                    查看简单提交历史记录
git log                       查看详细提交历史记录
```

其他命令

```bash
git rm --cached 文件名        将指定文件从暂存区中移出
git checkout 文件名           将暂存区中的文件覆盖工作目录上的
git restore 文件名						 将本地仓库中的文件恢复到工作区中
```





**补充：** 区分 `git add` 添加暂存区不同后缀的区别

| Git Version 2.x | 新文件 | 被修改的文件 | 被删除的文件 | 是否受当前所在目录限制 | 说明                                                         |
| --------------- | ------ | ------------ | ------------ | ---------------------- | ------------------------------------------------------------ |
| git add -A      | ✅      | ✅            | ✅            | ❌                      | 将当前整个工作区中所有的文件改动提交至暂存区，包括新增、修改和被删除的文件，不受当前所在目录限制 |
| git add .       | ✅      | ✅            | ✅            | ✅                      | 将当前工作区中当前目录(包括子目录)下的所有的文件改动提交至暂存区，包括新增、修改和被删除的文件 |
| git add -u.     | ❌      | ✅            | ✅            | ❌                      | 将当前整个工作区中被修改和被删除的文件提交至暂存区。而新文件因为未被跟踪(untracked)，所以不会被提交至暂存区 |
| git add *       | ❌      | ✅            | ✅            | ✅                      | 将当前工作区中当前目录(包括子目录)下的所有的文件改动提交至暂存区，包括新增、修改和被删除的文件，但不包括文件名以 `.` 符号开头的文件的改动 |



**补充：** 区分 `git commit` 不同后缀的区别

```bash
# 将暂存区内容提交到版本库, 进入 vim 命令界面输入提交信息
# vim 编辑器使用 修改第一行默认的提交信息，i：开始输入 esc：退出输入 
# 退出输入后 :q即可退出vim编辑器，或者输入:q!(强制退出不保存)、:wq(保存后退出)、:wq!(强制保存后退出)
git commit

# 将某些已被跟踪的文件提交到版本库（包含工作区和版本库）
git commit [file1] [file2] [...]

# 将暂存区内容提交到版本库, 无需进入 vi 命令界面输入提交信息
git commit -m [message]

# -a 后缀 表示跳过 git add, 将所有已被跟踪的文件更改直接提交到版本库，未追踪的不操作
git commit -am [message]

# 使用一次新的commit, 替代上一次提交
# 如果代码没有任何新变化, 则用来改写上一次commit的提交信息
git commit --amend -m [message]
```





## git撤销命令

git 撤销命令常用的有两个

```bash
git reset --hard commitID		回滚到指定版本版本，丢弃后面的提交
git push -f 地址 分支			 		回滚后版本号变旧需要强制推送

git revert commitID					重做到之前的一次版本，保留后面的提交
git commit -m 提交信息			 重做后再次提交，指定的版本提交内容就已经消失
```



## git分支命令

分支合并冲突时需要手动调整，调整完毕后再进行一次**暂存，提交。**

```bash
git branch -v                查看分支
git branch 分支名称           创建分支
git checkout 分支名称         切换分支
git merge 分支名称            将指定的分支合并到当前分支上
git branch -d 分支名称        删除已经合并的分支（-D强制删除）
```

切换分支前保存暂存区中的内容

```bash
git stash                     临时存储暂存区中的改动
git stash pop                 回复之前存储的暂存区
```



## github远程仓库命令

为远程仓库注册别名

```bash
git remote -v                     查看已经注册的仓库别名
git remote add 别名 远程地址        为远程仓库注册别名
```

远程命令

```bash
git push 地址 分支                  推送远程仓库
git pull 地址 分支                  拉取远程仓库，拉取前建议先执行commit提交本地库，再pull最新代码
git clone 地址                     克隆全程仓库到本地
```

多人合作时务必先拉去远程仓库的最新代码后再推送。



## .gitignore忽略文件

让git忽略指定的文件或文件夹，不进行版本控制。只需在隐藏的git文件夹同级目录下创建即可。

```bash
abc.txt							忽略指定文件
node_modules					忽略指定文件夹
node_modules/hello.js			忽略指定路径下的文件
css/*.js						忽略指定后缀名文件
```