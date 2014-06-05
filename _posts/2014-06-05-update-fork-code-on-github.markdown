---
layout: post
title:  "更新github上fork的代码"
---

一、前提

本文的前提是你已经在github上fork了别人的分支，并且弄好了跟github的ssh连接。相关配置详情参考：[github help][githubhelp]

例如你fork了dotcloud/docker这个repo到自己的仓库your/docker下

二、详细操作

检出自己在github上fork别人的分支到docker目录下。

```bash
git clone git@github.com:your/docker.git docker
```

然后增加远程分支（也就是你fork那个人的分支，这里是dotcloud/docker）名为dotcloud（这个名字任意）到你本地。

```bash
git remote add dotcloud https://github.com/dotcloud/docker.git
```

如果你运行命令：git remote -v你会发现多出来了一个dotcloud的远程分支。如下：

```bash
dotcloud https://github.com/dotcloud/docker.git (fetch)
dotcloud https://github.com/dotcloud/docker.git (push)
origin git@github.com:your/docker.git (fetch)
origin git@github.com:your/docker.git (push)
```

然后，把对方的代码拉到你本地。

```bash
git fetch dotcloud
```

最后，合并对方的代码。

```bash
git merge dotcloud/master
```

final，把最新的代码推送到你的github上。

```bash
git push origin master
```

这样就完成了自己的代码更新。


[githubhelp]: https://help.github.com