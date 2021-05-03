[TOC]

## 4个阶段的撤销
```
假设：
工作区:a
暂存区:b
HEAD:c

#git diff命令结论

git diff 比较a跟b
git diff --cached 比较b跟c
git diff HEAD 比较a跟c


#git reset跟 git checkout结论

git reset HEAD  <file>      c覆盖b
git checkout -- <file>      b覆盖a
git checkout HEAD <file>    c覆盖a,b

#git rm命令结论

git rm 删除a跟b
git rm --cached 只删除b
rm file 只删除a


```



如何撤销文件修改:

## 阶段一：已修改，未暂存

简单的讲就是修改了内容，但是还`未执行git add命令`，进行撤销操作。

例如：修改了readme.txt,但还未用git add 命令提交到暂存区,要想恢复到未修改状态,如何解决?

```
//使用如下命令
git checkout -- readme.txt(文件名)
//或者
git reset HEAD readme.txt(文件名)
```

> 备注:

命令git checkout -- readme.txt意思就是，把readme.txt文件在工作区的修改全部撤销，这里有两种情况：

一种是readme.txt自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；

一种是readme.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

总之，就是让这个文件回到最近一次git commit或git add时的状态。

## 阶段二：已暂存，未提交

简单的讲就是已经`执行了git add `.，但还`没有执行git commit -m "comment"`。这时候你意识到了错误，如何撤销?

```
git reset
git checkout .
//或者
git reset --hard
```

git reset只是把修改退回到了git add .之前的状态，也就是说文件本身还处于已修改未暂存状态，你如果想退回未修改状态，还需要执行git checkout .。

或许你已经注意到了，以上两个步骤都可以用同一个命令git reset --hard来完成。是的，就是这个强大的命令，可以一步到位地把你的修改完全恢复到未修改的状态。

图解

![1620058135333](C:\Users\lds\AppData\Roaming\Typora\typora-user-images\1620058135333.png)

图解Git



## 阶段三：已提交，未推送

简单的讲就是你`已经执行commit之后`，将代码提交到了你的本地仓库，然而你后悔了，这时如何撤销？



提交到本地仓库的代码一样也可以撤销，我们可以利用`git reset --hard <版本号>`命令来实现版本回退，该命令中的版本号有几种不同的写法：

```
git reset --hard origin/master
#还是这个git reset --hard命令，只不过这次多了一个参数origin/master，正如我们上面讲过的，origin/master代表远程仓库，既然你已经污染了你的本地仓库，那么就从远程仓库把代码取回来吧
```

或者使用如下方法回退

```
1.可以使用HEAD^来描述版本，一个^表示前一个版本，两个^^表示前两个版本，以此类推。
2.也可以使用数字来代替^，比如说前100个版本可以写作HEAD~100。
3.也可以直接写版本号，表示跳转到某一个版本处。我们每次提交成功后，都会生成一个哈希码作为版本号，所以这里我们也可以直接填版本号，哈希码很长，但是我们不用全部输入，只需要输入前面几个字符即可，就能识别出来。
```

## 阶段四：已推送

如果你`既git add了，又git commit了，并且还git push了`，这时你的代码已经进入远程仓库。如果你想恢复的话，还好，由于你的本地仓库和远程仓库是等价的，你只需要先恢复本地仓库，再强制push到远程仓库就好了：

```
git reset --hard HEAD^
git push -f
```

示例

看下面一系列的操作：
1.通过git log查看当前提交日志：

2.通过git reset HEAD^^向前回退两个版本：

3.查看日志，发现最后一次提交的版本号是695ce1fe,利用git reset —hard 695ce1fe命令回到回退之前的状态：


4.通过git reset —hard HEAD~1回到上一个版本：



## 版本回退

HEAD指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令git reset --hard commit_id。
穿梭前，用git log可以查看提交历史，以便确定要回退到哪个版本。
要重返未来，用git reflog查看命令历史，以便确定要回到未来的哪个版本。

## 小结

场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令git checkout -- file。

```git
git checkout -- file
```

场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令git reset HEAD file，就回到了场景1，第二步按场景1操作。

```git
git reset HEAD file
```


场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，使用版本回退，不过前提是没有推送到远程库。


