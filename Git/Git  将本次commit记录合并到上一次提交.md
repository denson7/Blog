适用情况：在本地仓库代码已经commit了一次代码，但是还未push代码，此时又修改了一点代码，为了减少提交日志，就可以使用本方法。
```
git commit --amend // 会打开vim编辑模式，进行编辑保存
git commit --amend --no-edit //不会进入vim编辑模式,直接提交
```