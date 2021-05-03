[TOC]
## log 命令
```
#显示log
$ git config --global alias.logg "log --graph --decorate --abbrev-commit --all" #相当于 git logg
#显示彩色简洁日志树
$ git config --global alias.lg "log --graph --pretty=format:'%Cred%h%Creset - %C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative"
$ git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```
如图所示

![1620057668636](C:\Users\lds\AppData\Roaming\Typora\typora-user-images\1620057668636.png)



### 查看commit图

```

git log --graph

```

### 查看更多提交信息

```

git log  //默认显示log信息

git log --stat  //显示更多信息

git log --oneline  //显示一行

git log --oneline --graph

//使用别名

alias gl='git log --oneline --all --graph --decorate'

```

### 定制log输出

定制提交格式 --pretty=format:

```

//例如

git log --pretty="%h, $cn, %cr"

```

选项 说明 
- %H   提交对象（commit）的完整哈希字串 
- %h   提交对象的简短哈希字串 
- %T   树对象（tree）的完整哈希字串 
- %t   树对象的简短哈希字串 
- %P   父对象（parent）的完整哈希字串 
- %p   父对象的简短哈希字串 
- %an  作者（author）的名字 
- %_ae  作者的电子邮件地址 （由于新浪博客显示问题，请去除 %_ae 中的 _ ）
- %_ad  作者修订日期（可以用 -date= 选项定制格式）（由于新浪博客显示问题，请去除 % ad 中的 _ ）
- %ar  作者修订日期，按多久以前的方式显示 
- %cn  提交者(committer)的名字 
- %_ce 提交者的电子邮件地址（由于新浪博客显示问题，请去除 %_ce 中的 _ ）
- %_cd  提交日期 （由于新浪博客显示问题，请去除 %_cd 中的 _ ）
- %cr  提交日期，按多久以前的方式显示 
- %d:  ref名称
- %s:  提交的信息标题

- 其他: git log支持的选项很多,详细参考git log --help


