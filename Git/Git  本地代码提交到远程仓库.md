命令总结

```
git remote               查看远程库的信息  
git remote -v            显示更详细的信息  
git remote add origin git@xx.xx.xx.xx:repos/xxx/xxx/xxx.git  连接远程仓库  
git clone git@xx.xx.xx.xx:repos/xxx/xxx/xxx.git            复制一份远程仓库项目到本地  
git remote rm origin     关闭连接远程仓库  
git push origin master   推送分支  
git pull origin master   抓取更新
```

删除跟远程仓库的连接

```
git remote rm origin
注意，断开跟远程的连接后，如果还想连上，则需要再来一次
git remote add origin git@xx.xx.xx.xx:repos/xxx/xxx/xxx.git
```



示例

```
git init
git add .
git commit -am "###"      
//以上3步只是本地提交
git remote add origin git@xx.xx.xx.xx:repos/xxx/xxx/xxx.git
git push origin 本地分支:远程分支
```

如果遇到错误提示如图所示


执行如下命令后,重新推送到远端

```
git config http.postBuffer 524288000
```






