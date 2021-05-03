[TOC]

## 不确定哪个分支有自己提交的 commit
例如：某个提交先后合并到了各个分支上，但后来发现提交的这个修改是有问题的，需要排查到底哪个分支包含这个提交，然后将其修复掉。需要先确定有问题的提交的 commit-id, 例如 2d21b88

```
// 命令：git branch --contains <commit-id>
// 例如, dev，master, staging分支均包含这个提交
$ git branch --contains 2d21b88
* dev
  master
  staging
```