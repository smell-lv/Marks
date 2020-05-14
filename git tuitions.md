# 1. 向当前文件夹中添加git管理

- git init

# 2. 将该文件夹关联到某个GitHub版本库,下载 和上传
- git remote add origin https://github.com/smell-lv/Marks.git

注意大小写

- git pull origin master

获得master分支

- git push origin master

将master分支推送至远程

# 3. 一些例行配置

## 3.1. 忽略工作空间中的文件夹、过程文件、类型文件

在根目录里创建.gitignore文件，写入文件夹、文件名,也可以是*.txt之类的，告诉git不再监视这些内容。
此后可以自由地修改文件中的内容，要修改只需要git add *即可，并且文件夹有文件时直接创建二级目录，没有后删除二级目录

参考[GitHub](https://github.com/github/gitignore)。

# 4. 工作区和暂存区

git有三个操作对象，工作区、暂存区和版本库。
工作区指本地目录，暂存区是版本库的副本，版本库是按分支、版本mark存档的不同版本，本节只讨论前两者。

- git status

查看工作区与暂存区的差别,unmodified为修改没有被提交至暂存区,modified为修改已经被提交（也即工作区和暂存区里的该文件相同）

- git diff (fileName)

观察文件里的内容与暂存区里的区别

- git checkout -- fileName

用暂存区覆盖目录文件（工作区），用以回退未经add的代码，根据后面的经验，也可以用git reset --hard HEAD来做，更加直观。

- git add *

将目录文件（工作区）覆盖暂存区，用以提交代码（但不进入版本库）

# 5. 暂存区和版本库

- git commit -m 'version name'

将暂存区里的文件提交至版本库的最新版本，并记录版本名称

暂存区通过游标在版本库里不同分支、版本之间游走，可以向版本库新增新的版本，也可以回退至版本库以前的版本。

- git reset (--hard) HEAD(^^^)

将暂存区里的文件返回到版本库的某一版本,带上--hard则把工作区内容也改了

- git reset (id)

将暂存区里的文件返回到版本库的某一版本,带上--hard则把工作区内容也改了

# 6. 分支

- git branch test

创建名为test的分支

- git checkout test

切换到test分支，如果之前的工作区有更新的话是不能切换的，也即切换分支操作是最高权限，是在代码级别修改之上的。

- git checkout master

- git merge test

切换回原分支，并将test分支的最新版本合并回去

- git merge --no-ff -m "merge with no-ff" test

将test分支合并到现在的分支，但从树种可见test分支

- git branch -d test

- git push origin --delete test

删除本地test分支和远程test分支

通常操作为从master上创建新的分支，并在新的分支上修改、add、commit，测试无误后返回master并将新分支的最新版本merge回去。

如果是GitHub，每个分支是单独push的，即可以在本机用多个分支来工作，但只把测试无误的master分支放在GitHub上管理；
当然也可以把所有分支都放上去，但GitHub上的分支需要登录到网页上手动删除。

# 7. log

- git log --oneline

只显示所有版本的ID和comment

- git log --oneline -n 10

只显示最近10条的ID和comment

- git log --graph --oneline

使用树的方式显示所有分支的ID和comment

- git reflog

记录最近的操作及其结果（log）除外，对于返回以前的版本再回退回来很有用

- git log --graph --online >log.txt

将该树存储到log.txt文件下，经常这样做可以不丢失版本ID