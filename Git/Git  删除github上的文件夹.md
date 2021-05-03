将本地代码提交到github时,修改了一个文件名名字,再次提交后,github并没有覆盖原来的文件夹,而是保留第一次递交的文件夹,想要删除github上的文件夹,保存与本地同步,应删除本地缓冲,示例

```
//git rm -r --cached 删除文件夹
git rm -r --cached magento2-sample-api-module/SIlk/
git commit -m "Remove the now ignored directory some-directory"
git push origin master:master //再次推送到远程代码库
```

