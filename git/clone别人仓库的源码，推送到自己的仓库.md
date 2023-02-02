# github上clone别人仓库的源码，如何推送到自己的仓库

如果你从github上clone下来一个别人的仓库，在此基础上完成你的代码，推送到自己的仓库可能会遇到如下问题：`error: remote origin already exists.`表示远程仓库已存在。

因此你需要进行以下操作：

1、输入git remote rm origin删除关联的origin的远程库

2、关联自己的仓库git remote add origin https://github.com/xxxxxx.git

3、重命名当前分支git branch -M master

4、最后git push -u origin master，这样就推送到自己的仓库了。