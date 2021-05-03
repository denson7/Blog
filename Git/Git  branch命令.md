[TOC]
## branch 命令
### 查看所有的本地分支
```
git branch
```
### 查看所有的远程分支
```
git branch -r
```
### 查看所有的本地分支和远程分支
```
git branch -a
```

### 创建新分支
```
git branch [branch_name]
// 创建新分支并切换到该分支
git checkout -b [branch_name]
// 新建一个分支，并指向某个commit
git branch [branch_name] [commit_id]
// 新建一个分支，并与指定的远程分支绑定关系
git branch --track [branch_name] [remote_branch]
// 建立追踪关系，在当前分支与指定的远程分支直接
git branch --set-upstream [branch] [remote_branch]
```
### 切换分支
```
// 切换到指定分支
git checkout [branch_name]
// 切换到上一个分支
git checkout -
```
### 合并分支
```
git merge [branch_name]
```
### 修改分支名
```
// 语法
git branch -m <oldbranch> <newbranch>
// 示例
git branch -m feature/tes feature/test
```
### 删除分支
```
git branch -d [branch_name]
// 强制删除,未被合并的分支被删除时需要强制删除
git branch -D [branch_name]
```
### 删除远程分支
```
git branch -dr [remote/branch_name]
$ git branch -r -D origin/BranchName
// 或者
git push origin --delete [branch_name]
$ git push origin --delete feature/emr_2.1.0
```
### 远程分支删除后，删除本地分支及关联
```
一般情况下会用本地命令 git branch --set-upstream-to=origin/master master 建立本地分支与远程分支的关联, 但Git 远程分支删除之后，本地分支就无法成功推送到远程，想要重新建立与远程仓库的关联，就需要先删除其原本的与已删除的远程分支的关联。
// 1.删除远程分支
git push origin --delete feature/test 
// 2.删除远程分支之后，删除本地分支关联 (git branch --unset-upstream <branchname>)
git branch --unset-upstream feature/test 
// 3.删除关联关系之后，用 git branch -vv 命令可查看到本地分支已没关联的远程分支
```

