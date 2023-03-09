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
git rm --cached 文件名        将文件从暂存区中移出
git checkout 文件名           将暂存区中的文件覆盖工作目录上的
```



## git撤销命令

git 撤销命令常用的有两个

```bash
git reset --hard commitID		回滚到指定版本版本，丢弃后面的提交
git push -f 地址 分支			 回滚后版本号变旧需要强制推送

git revert -n commitID			撤销到之前的一次版本，保留后面的提交
git commit -m 提交信息			 撤销后再次提交，指定的版本提交内容就已经消失
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
git pull 地址 分支                  拉取全程仓库
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