# Git常用命令速查表

## 创建版本库

git clone <url\>

    克隆远程版本库

git init

    初始化本地版本库

## 提交与修改

git status

    查看状态 

git diff

    查看变更内容

git add . 

    跟踪所有改动过的文件

git add <file\>
    
    跟踪指定的文件

git mv <old\> <new\>

    文件改名

git rm <file\>

    删除文件

git rm  --cached <file\>

    停止跟踪文件但不删除

git commit -m "commit message"

    提交所有更新过的文件

git commit -amend

    修改最后一次提交

## 查看提交历史

git log

    查看提交历史

git log -p <file\>

    查看指定文件的提交历史

git blame <file\>

    以列表方式查看指定文件的提交历史

## 撤消

git reset --hard HEAD 
    
    撤消工作目录中所有未提交文件的修改内容

git checkout HEAD <file\>

    撤消指定的未提交文件的修改内容

git revert <commit\>

    撤消指定的提交

## 分支与标签

git branch

    显示所有本地分支

git checkout <branch/tag\>

    切换到指定分支或标签

git branch <new-branch\>

    创建新分支

git branch -d <branch>

    删除本地分支

git tag

    列出所有本地标签

git tag <tagname\>

    基于最新提交创建标签

git tag -d <tagname\>

    删除标签

## 合并与衍合

git merge <branch\>

    合并指定分支到当前分支

git rebase <branch\>

    衍合指定分支到当前分支

## 远程操作

git remote -v

    查看远程版本库的信息

git remote show <remore\>

    查看指定远程版本库信息

git remote add <remote\> <branch\> <url\>

    添加远程版本库

git fetch <remote\>

    从远程版本库获取代码

git pull <remote\> <branch\>

    下载代码及快速合并

git push <remote\> <branch\>

    上传代码及快速合并

git push --tags

    上传所有标签

## 设置代理

git config --global http.proxy http://proxy.example.com:8080

## git ignore

在git项目下，创建.gitignore配置文件，配置语法如下

* 以斜杠"/"开头表示目录
* 以星号"*"通配多个字符
* 以问号"?"通配单个字符
* 以方括号"[]"包含单个字符的匹配列表
* 以叹号"!"表示不忽略(跟踪)匹配到的文件或目录

示例为

```shell
# 注释

*.a # 忽略所有.a结尾的文件
!lib.a # 除lib.a除外
/TODO # 仅忽略项目根目录下的TODO文件，不包括subdir/TODO
build/ # 忽略build/目录下的所有文件
doc/*.txt # 忽略doc目录下所有的.txt文件
```

如果某些文件已经被纳入了版本管理，则必须先把本地缓存状态删除，将文件都改为untrack状态，然后再提交

```shell
git rm -r --cached . 
git add .
git commit -m "update .gitignore"
```