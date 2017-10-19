---
layout: post
title: Git Global Ignore
---
Git可以配置global的ignore, 姿势如下：

1. 配置git使用global的ignore文件

```git config --global core.excludesfile ~/.gitignore_global 
```

1. 添加你自己的gitignore的rule至~/.gitignore_global文件, 比如:

```console/conf.js```

1. 坑: global ignore只ignore新文件，不会ignore已经存在的文件，如果你想对已经存在的文件生效，尝试

```git update-index --assume-unchanged console/conf.js```
