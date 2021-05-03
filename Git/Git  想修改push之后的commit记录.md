## 想修改push之后的commit记录
例如：commit之后发现commit中有单词拼写错误，这个必须得修改，该如何操作呢？
1.回退到最近的1次commit
```
git rebase -i HEAD~1
```
2.修改pick为edit
```
修改commit前的pick为edit,然后:wq保持退出
```
3.修改commit内容
```
git commit --amend
```
4.修改之后，执行如下命令
```
git rebase --continue
```
5.强制push
```
git push --force
```
