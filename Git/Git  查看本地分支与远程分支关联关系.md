[TOC]
## Git 查看本地分支与远程分支的关联关系
git在本地新建一个分支以后，必须要做远程分支关联。如果没有与远程分支进行关联的话， 在进行git pull和git push的时候，会报错。创建本地分支与远程分支关联关系的目的是在进行push或pull时，不需要在命令行显示的指定远程分支名称，直接将代码推送到远程分支。只要没有显示指定，再进行git pull 的时候，都会提示你。
`解决：`
```
// 使用命令 git branch --set-upstream ,例如本地新建了一个demo分支，
git branch --set-upstream demo origin/demo
// 如果是新建的一个分支，远程分支也没有，就直接推送本地分支到远程
git push origin demo
```
何查看已经配置分支关联信息呢?
```
// 方法一
git branch -vv
// 方法二
git remote show origin
// 方法三
cat .git/config
```
