[TOC]
## 合并某个分支的某个commit
命令：cherry-pick
作用：将指定的提交（commit）应用于其他分支。
### 合并单个commit
场景：例如要将develop分支的一个commit合并到master分支
```
a - b - c - d   Master
     \
      e - f - g develop
// pick 后
a - b - c - d - f   Master
     \
      e - f - g develop
```
```
// 切换到develop分支
git checkout dev
// 找到需要合并的commit, 如：bfec95a
git log
// 然后切换到master分支
git checkout master
// 使用cherry-pick命令合并单个commit
git cherry-pick bfec95a
```

### 合并多个不连续commit
```
// 将 A 和 C 两个提交应用到当前分支，会在当前分支生成两个对应的新提交。
git cherry-pick <hashA> <hashC>
```
### 合并多个连续的commit
```
// 转移从 A 到 E 的所有提交，提交 A 必须早于提交 E，注意此命令不包含提交A
git cherry-pick A..E 
// 包含提交A，使用如下命令
git cherry-pick A^..E
```


### 代码冲突
```
// 代码冲突，查看状态
git status 
// 解决冲突后，将修改的文件重新加入暂存区（git add .），再使用如下命令，让 Cherry pick 过程继续执行使用命令继续执行。
git cherry-pick --continue
// git cherry-pick --abort 发生代码冲突后，放弃合并，回到操作前的样子。
// git cherry-pick --quit 发生代码冲突后，退出 Cherry pick，但是不回到操作前的样子。
```