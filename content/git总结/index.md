+++
title = "git总结"
date = 2021-10-07 17:45:12
slug = "202110071745"

[taxonomies]
tags = ["Git"]
categories = ["Git"]

+++

<!-- more -->

## 配置

#### .gitignore

```
# Java
*.class
 
# Eclipse
.classpath
.project
.settings/
 
# Intellij
.idea/
*.iml
*.iws
out/
 
# Mac
.DS_Store
 
# Maven
log/
target/
 
# svn
.svn/
 
scandir/
rebel.xml
 
#lilei environment
dep2.sh
 
#log
*.log
```

## git rebase

### 合并本地commit

```
$ git rebase -i [startpoint] [endpoint]
// -i 的含义是：--interactive, 即弹出交互式的界面让用户编辑完成合并操作
// [startpoint] 指的是合并区间的起点。
// [endpoint] 指的是合并区间的终点，默认是当前分支 HEAD 所指向的 commit。
// 这里的区间是一个前开后闭的区间。
```

1. 编辑 commit 内容。将 "pick" 改成 "squash" 或者 "s"，意思是将该 commit和 前面的 commit合并。修改合并之后的 commit 注释。编辑完成后，保存并退出（wq!）。
2. 完成上一步后进入了一个临时的分支（从 development 分支切出来的临时分支）。基于这个临时分支，创建一个新的分支 `git checkout -b feature/temp`。
3. 再切回 development 分支，执行：git rebase feature/temp合并分支。

## git commit --amend

commit message信息修改

执行`git commit --amend`出现编辑界面（vim），编辑方法同Linux下的vim，编辑后保存并退出

