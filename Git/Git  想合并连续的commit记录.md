## 想合并几个连续的commit记录为1个commit
```
git rebase -i xxxx(修改commit的前一次的commitId) // 使用squash参数
// 这里面的commitId是你要合并的两个commit后所形成的一个新的commitId需要跟着的commitId。
// 例如有三个commitId (commit1,commit2,commit3)，现在需要合并commit2和commit3，那么commitId就应该指向commit1对应的commitId。
// -i 参数表示不需要合并的 commit 的 hash 值，这里指的就是 commit1对应的commitId。
```

![1620057419066](C:\Users\lds\AppData\Roaming\Typora\typora-user-images\1620057419066.png)

