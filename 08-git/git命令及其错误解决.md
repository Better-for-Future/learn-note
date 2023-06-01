# 解决Git中fatal: refusing to merge unrelated histories

2018-06-29 73244举报

**简介：** Git的报错 在使用Git的过程中有时会出现一些问题，那么在解决了每个问题的时候，都需要去总结记录下来，下次不再犯。 一、fatal: refusing to merge unrelated histories 今天在使用Git创建项目的时候，在两个分支合并的时候，出现了下面的这个错误。

[![img](https://img.alicdn.com/imgextra/i3/O1CN01CFSsoH1z6ADDjfMru_!!6000000006664-2-tps-1480-120.png)](https://free.aliyun.com/)

## Git的报错

在使用Git的过程中有时会出现一些问题，那么在解决了每个问题的时候，都需要去总结记录下来，下次不再犯。

## 一、`fatal: refusing to merge unrelated histories`

今天在使用Git创建项目的时候，在两个分支合并的时候，出现了下面的这个错误。

```
~/SpringSpace/newframe on  master ⌚ 11:35:56
$ git merge origin/druid
fatal: refusing to merge unrelated histories
```

这里的问题的关键在于：`fatal: refusing to merge unrelated histories`
你可能会在`git pull`或者`git push`中都有可能会遇到，这是因为两个分支没有取得关系。那么怎么解决呢？

## 二、解决方案

在你操作命令后面加`--allow-unrelated-histories`
例如：
`git merge master --allow-unrelated-histories`

```
~/SpringSpace/newframe on  druid ⌚ 11:36:49
$ git merge master --allow-unrelated-histories
Auto-merging .gitignore
CONFLICT (add/add): Merge conflict in .gitignore
Automatic merge failed; fix conflicts and then commit the result.
```

如果你是`git pull`或者`git push`报`fatal: refusing to merge unrelated histories`
同理：
`git pull origin master --allow-unrelated-histories`
等等，就是这样完美的解决咯！