title: git 高阶用法
tags:
- git
layout: post
categories: Tool
date: 2017-07-23
---
## 日常生活中的git

通常情况下，在进行git操作的时候，大家最熟悉的流程：

![](http://7fvhwe.com1.z0.glb.clouddn.com/git_route.png)

可能还有的同学也经常`git commit --amend -C head`，向上一个commit提交内容。
然而在日常生活中总会有一些其他对代码版本管理的需求，比如需要去修改很久之前的一个commit的内容等。

本文主要记录一些git相对使用较少，但是非常有用的命令。


## git rebase

git rebase 有两个常用功能：
- 从上游分支获取最新commit信息，并有机的将当前分支和上游分支进行合并。
- 对当前分支的历史commit进行修改，合并，删除等操作

<!-- more -->

### 场景

- 你的分支和master冲突

如果你`git merge master`就会留下一个merge的commit。在这种情况下，我们推荐使用`git rebase master`，就能不留commit将当前分支和master有机的合并。

- 之前某次提交，改错了一个变量

如果你修改那个变量然后再提交一个commit，这显然不是最优的。如果能将某次的提交挑出来修改就好了。当然git提供了这个功能，你可以使用`git rebase -i [git-hash| head~n]`,其中`git-hash`是你要开始进行rebase的commit的hash，而head~n则是从HEAD向前推n个commit.

当你执行`git rebase -i `你会看到如下界面，需要你去选择对应的commit指定一种操作。下面提示了很多操作项。

![](http://7fvhwe.com1.z0.glb.clouddn.com/Screen%20Shot%202017-06-24%20at%2010.03.41%20PM.png)

## git stash

git stash 主要功能是：
- 暂存当前没有提交的更改
``` shell
git stash save 保存当前更改
git stash 保存当前更改
git stash pop 推出之前stash的内容更改
git stash apply 推出之前stash的内容更改

git stash save --keep-index 只stash没有被add的内容
git stash save --include-untracked stash还未加入git记录的文件

git stash list --stat 显示stash堆栈
git stash show stash@{0} 显示第x次的更改
git stash show 显示最近一次stash的更改
git stash show --patch 显示最近一次stash的详细更改

git stash save "stash msg"

git stash branch new_brach stash@{0}

```

### 场景

- 当做了更改之后但是没有编写完，这个时候发现线上有个bug，你需要停下当前分支去修bug，这个时候你可以提一个commit到当前分支，或则使用`git stash`

stash多次是以堆栈的形式进行存储的。

## git filter-branch
git filter-branch的主要功能是
- 过滤所有提交记录，进行相应的操作

```
git filter-branch --tree-filter 'rm -rf .vscode' 删除所有分支的.vscode文件夹
git filter-branch --tree-filter 'rm -f xxx' xxx不存在，不报错
git filter-branch --tree-filter 'rm -rf .vscoe' -- --all(所有分支所有提交)

git filter-branch --index-filter 'git rm --cached --ignore-unmatch password.txt' (只检查password.txt 一个文件)
git filter-branch -f --prune-empty -- --all
```

### 场景

- 你发现你错误的将一个不应该提交的文件提交，然后已经过了很久，已经累计了无数次提交。

这个时候就可以使用git filter-branch将所有提交全部过滤一遍删除掉那个不应该提交的文件

## cherry-pick
git cherry-pick的主要功能是
- 从任何分支，抽取提交到当前分支

```
git cherry-pick git-hash 

git cherry-pick --edit git-hash

pick 多个
git cherry-pick --no-commit git-hash git-hash

git cherry-pick -x git-hash (添加cherry pick from 那个branch)
git cherry-pick --signoff git-hash不修改author
```

### 场景

当需要将多个分支上的提交合在一个分支合并到master的时候，显然如果这3个分支有关联，一次合并更为合理。
这个时候就可以使用`git cherry-pick`进行精细化的commit的操作。

## work together

git config --global core.autocrlf
linux osx是lf
windows 是crlf

如果你的源文件中是换行符是LF，而autocrlf=true, 此时Git add就会遇到 fatal: LF would be replaced by CRLF 的错误。有两个解决办法：
1. 将你的源文件中的LF转为CRLF即可【推荐】
2. 将autocrlf 设置为 false

如果你的源文件中是换行符是CRLF，而autocrlf=input,  此时git add也会遇到 fatal: CRLF would be replaced by LF 的错误。有两个解决办法：
1. 将你源文件中的CRLF转为LF【推荐】
2. 将autocrlf 设置为true 或者 false


.gitattributes
```
* text = auto 
*.html text
*.css text

*.jpg binary
*.png binary

*.ssh text eol=lf
*.bat text eol=crlf

```

## 一些可能会用到的

### git submodule
```
git submodule add git@example.com:css.gi
```

### git reflog
```
git log --walk-reflogs
```