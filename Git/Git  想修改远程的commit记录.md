[TOC]

## 1.修改的是最近的一次提交
```
第一步：执行git commit --amend
第二步：进入vim模式，按 I(进入编辑状态),然后修改commit信息

第三步：按一下ESC键，退出vim编辑模式，再按:wq退出vim模式
第四步：git push origin 分支名 -f
```
## 2.修改历史记录的commit
```
//例如，总共提交了3此，现在要修改倒数第2个commit记录
第一步：git rebase -i HEAD~2 (因为这里我要查看2个就行,所以用2,想查看多少个commit,就写多少个)

第二步：按 i 键进入编辑模式，将需要修改的commit的pick变成edit，然后直接按esc键，然后:wq退出

第三步：接下来会提示如果你要修改就执行git commit --amend 一旦你觉得修改满意,就执行git rebase --continue ，假如本次修改不满意，继续执行git commit --amend

第四步：按i键进入编辑模式，再次修改你的commit内容，修改完成之后再按esc退出编辑模式，最后:wq 退出vim模式

第五步：假如这一次修改满意，继续执行：git rebase --continue

第六步：强推所在分支执行：git push origin 分支名 -f

```
