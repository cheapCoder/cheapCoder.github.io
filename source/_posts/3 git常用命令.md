---
title: git常用命令
date: 2020-09-30 15:15:01
tags: 
- git
---

##### 集中式和分布式版本控制系统的区别
>分布式版本控制系统：每个开发者都有整个代码库的所有版本，在离线状态下开发者可以进行版本管理开发, 等网络恢复再push到仓库中。

>集中式版本控制系统：每个开发者只有应用代码库的一个版本，在离线状态下开发者无法进行版本管理开发。

![Git 工作区、暂存区和版本库](https://www.runoob.com/wp-content/uploads/2015/02/1352126739_7909.jpg)

1. ###### 基本

`git init`:初始化仓库
`git status` : 查看状态：红色表示在工作区，绿色表示在暂存区，其他在版本区
`git add 文件名`  :工作区添加到暂存区，"."号表所有
`git commit -m '注释'` : 暂存区到版本区
`git clone URL` : 克隆仓库
`git remote add 别名 URL`: 本地仓库关联远程仓库,别名一般用origin
`git remote remove 别名`: 本地仓库移除关联

2. ###### 分支操作

`git checkout 分支` : 切换分支
`git checkout -b 分支` : 创建并切换
`git branch 分支` : 创建分支但不切换
`git branch`: 查看分支列表
`git checkout -d 分支` : 删除分支
`git push origin 分支` : 分支推送远程
`git pull origin 分支` : 远程拉取到本地
`git fetch origin master:tmp`: 新建一个tmp分支，将远程仓库的master分支上代码版本复制到tmp分支上，不会自动合并
`git merge 分支` : 指定分支合并到当前分支

3. ###### 版本回退
`git log`: 提交日志
`git reflog`: 每次提交的ID
`git reset --hard HEAD^`: 版本回退到一个版本，"\^"回退一个版本，"^^"回退两个版本
`git reset --hard ID号`:回退到指定版本

4. ###### 删除文件
`git rm 文件名`: 删除指定文件
`git rm -r 文件名`: 删除指定文件夹
`git rm --cached 文件`: 从暂存区删除 

5. ###### 比较差异

`git diff` : 比较暂存区与工作区
`git diff --cached` : 比较版本区与暂存区
`git diff master` : 比较版本区与工作区
`git diff branch1 branch2`:显示出两个分支之间所有有差异的文件的详细差异
`git diff branch1 branch2 --stat`:显示出两个分支之间所有有差异的文件列表
`git diff branch1 branch2 xxx`:显示指定文件的详细差异

<hr>

###### 补充基础Linux命令

`mkdir` :创建文件夹 
`vi` : 创建文件并进入
`i` :编辑模式
`ESC+:+wq` : 保存并退出
`ESC+:+q!` : 不保存并退出
`cd` : 进入文件夹
`ls` : 当前文件夹文件列表
`pwd` : 当前目录
`cat` 文件 : 显示文件内容
`clear` : 清屏

###### git flow
  1. 日常开发：feature => dev => test => master
  2. 紧急修复bug：master => hotfix => test => master