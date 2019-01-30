---
title: git开发流程及常用操作
date: 2018-12-25 17:36:35
tags: [git]
categories: [技术,git]
---

# github开发流程：
第一步：切换到develop分支：git checkout develop。
第二步：从develop分支迁出本地开发分支:git checkout -b developforwindy420
第三步：在developforwindy420进行开发后，需要提交提交代码：git add .; git commit -m "{你的开发功能}"
第四步：切换到develop分支，对developforwindy420进行合并：git checkout develop; git merge --no-ff developforwindy420
第五步：提交代码到develop分支:git pull; git push
第六步：代码提交后，此次开发分支作废，需要删除：git branch -d developforwindy420

## github修改MASTER的BUG流程：
修复BUG流程：
第一步：切换到master分支:git checkout master
第二步：从master分支上创建本地分支：git checkout -b fixedbug420
第三步：在fixedbug420分支上对bug进行修复，进行commit提交:git add .; git commit -m "{你所修复的BUG}"
第四步：需要将fixedbug420的修复merge到master和develop分支上。具体操作如下：git checkout master;git merge --no-ff fixedbug420; git pull; git push。  git checkout develop;git merge --no-ff fixedbug420; git pull; git push。
第五步：bug修复后，需要删除本地分支:git branch -d fixedbug420

# git常用功能
## git回退到某个版本并提交
回退到分支某个版本  git reset --hard  c88173be7b521fcbe88c5be4fc1d3889e52226ae（要回退的分支版本号）
查看分支当前版本  git rev-parse HEAD 
提交回退的版本 git push -f  (注：强制提交后，当前版本后面的提交版本将会删掉！)

## git分支
git branch 查看分支
git branch xxx 创建分支
git branch -d xxx 删除分支

## git修改暂存
git stash :保存当前工作区和暂存区的修改。

git stash save “描述信息” :如果保存的内容过多可能会导致无法辨别需要恢复的保存记录，这时候就加上 save参数后面双引号后面填写此次保存的描述信息。

git stash list : 显示保存列表。

git stash pop : 恢复最近保存的记录并把恢复的记录从保存列表中删除。只恢复工作区！（默认会将被恢复的操作保留在工作区，但是不会自动帮你重新暂存）

git stash pop --index ：与上面命令的效果一样但是还会恢复暂存区！

git stash pop stash@{序号} ：恢复保存列表里面指定的保存记录，并把恢复的记录从保存列表中删除。

git stash --patch :保存之前先比较工作区和HEAD之间的区别，然后选择是否重新编辑需要保存的文件。

git stash apply ：恢复最近保存的记录但不会删除保存列表里面对应的记录。（默认会将被恢复的操作保留在工作区，但是不会自动帮你重新暂存）

git stash drop :删除保存列表里面最近保存的记录。后面加上 stash@{序号}可以删除指定的保存记录。

git stash clear :删除保存列表里面所以的保存记录。

git stash 分支名 stash@{序号}：修改了demo里面的内容之后使用 git stash 保存demo文件里面的修改，如果此时你修改了demo文件然后在使用git stash pop或者git stash apply 恢复之前修改demo里的内容，那么此时可能会引发冲突。此时就可以使用此命令。这个命令会创建一个分支然后在创建的分支上把保存的记录恢复出来。
