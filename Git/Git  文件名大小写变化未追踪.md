当修改了文件名大小写后重新提交代码，结果发现git status中并未找到该变化，究其原因是默认git配置了忽略大小写敏感。

```powershell
// 查看本地仓库git忽略大小写是打开还是关闭
$ git config core.ignorecase // 返回true
// 设置大小写敏感，但是不建议使用，因为以免之后合并分支等操作因为大小写引起的冲突
$ git config core.ignorecase false
// 推荐使用
$ git mv -f oldFile newFile
// 如果新文件夹已存在于您的存储库中，并且您想覆盖它并使用： - force
git mv -f oldFile newFile
```


