---
title: gitCommand
date: 2021-01-06 12:40:36
tags:
---
# git command

### git-resotre 和git-reset的区别

* git restore用于处理工作区的文件，命令用于将在工作区，但不在暂存区的文件还原。

    git restore --staged用于将已经提交到缓存区的文件，从缓存区拉出，但不还原文件。

* git reset用于处理分支的提交记录，可以将当前指针在几个提交记录间切换。