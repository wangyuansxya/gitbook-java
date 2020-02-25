**git常用命令**

1. git init：初始化本地仓库

2. git remote add origin <url地址> 关联远程仓库

3. git config相关介绍

$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com
如果用了 --global 选项，那么更改的配置文件就是位于你用户主目录下的那个，以后你所有的项目都会默认使用这里配置的用户信息。如果要在某个特定的项目中使用其他名字或者电邮，只要去掉 --global 选项重新配置即可，新的设定保存在当前项目的 .git/config 文件里

git config --list：查看一下当前具体使用了哪些配置信息

4. git pull :从远程仓库拉取代码

5. git push：从本地仓库推送代码到远程仓库

6. git cherry-pick <commit-id>..<commit-id> 从一个分支一部分commit复制到另一个分支

7. git rebase 合并分支。注意和merge的区别

8. Mac 删除git文件夹,删除svn文件夹

cd到该文件夹

    //删除文件夹下的所有 .svn 文件

    find . -name ".svn" | xargs rm -Rf

    //删除文件夹下的所有 .git 文件

    find . -name ".git" | xargs rm -Rf

