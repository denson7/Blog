## 想合并已经push后的commit记录
1.查看commit历史
```
git log
```
2.选择要合并的commit，2表示合并最新的2条commit
```
git rebase -i HEAD~2
```
3.选择要保留的commit和要合并的commit
```
pick表示选择这个commit，squash表示合并这个commit
```
4.修改commit的文字描述
```
上一步修改完成后，按esc键，冒号，输入wq进行保存。之后会继续跳转到commit message 的编辑界面，注释中有写第一个commit信息和第二个commit信息，这里可以修改成一个新的commit信息，也可以不修改，不修改的话，commit信息就前两次的commit信息都会展示出来。
```
5.再次执行git push推送到远程仓库
```
git push origin  -f
```
