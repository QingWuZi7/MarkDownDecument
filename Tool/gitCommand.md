# Git

- 在一个非空文件夹中建立git仓库

  `$ cd c/qw/er`

  `$ git init`

- 克隆仓库

  `$ git colne https://github.com/a/b`

  创建名为b的文件夹，初始化`.git`文件夹，项目文件拷贝完成。

  `$ git colne https://github.com/a/b myName`

  目标目录名变成myName

  

- 仓库操作

  `git add xx` `git README` 

  `git commit -m "desc"`

  `git status` 

  `git checkout source -- fileName`复制a文件到main分支（先切换到main）

  `git rm -r file`

  
  
  ![](C:\Users\Cho\Desktop\git1.png)
  
  > git add添加文件，即把文件修改添加到暂存区。git commit提交更改，即吧暂存区所有内容提交到当前分支。

好的习惯——为仓库创建ignore分区

```powershell
$ cat .gitignore
*.[oa]
*~
```

mean:

忽略所有以o或a结尾的文件，一般这类文件（对象文件 存档文件）都是编译过程中出现的。

忽略所有名字以~结尾的文件，很多文本Editor以此保存副本。

- 上传到远端仓库

`git push origin master`

master指分支，一定要注意push的分支要正确。

否则会发生timeOut的错误？

- 从远端更新

  `git pull`

  `git branch`查看分支

  `git checkout xx`	切换分支

  `git branck xx`创建分支

  `git restore`还原

  `git checkout -[fileName]`更新文件。建议先merge完成




- 删除（要先切换到其他分支）
  - `git branch -d localName`删除本地分支(被改动了会提示需要先合并,用-D)
  - `git branch -D localName`强制删除
  - `git push origin --delete remoteName`删除远程分支



	- 查看日志

​	`git log`

​	`git log -g`查看最近一次的commit_id



- 恢复（trancked就是add）

​	`git checkout -- file` 还原修改的文件(未tracked)，对已tracked文件撤销修改。

​	`git rm --cached file` 已tracked未commit文件 => untracked

​	`git rm -f file` 从本地删除已add的文件

​	`git reset HEAD file` 撤销commit，回到modified状态

​	`git reset --soft HEAD` 撤销commit，回到tracked状态

​	`git clean` 删除所有untrancked文件。

- `git clean 参数` ——不指定路径参数时会对当前目录操作。
  - -n 演习？
  - -f 删除当前目录下的untracked文件(除了.gitignore文件)
  - -f <path> 指定路径下
  - -df 删除当前目录下untracked文件和文件夹 （比较：`git rm -f file`）
  - -xf 删除当前目录下所有untracked文件，包括.gitignore

​	`git reset --hard` `git clean -f` 使本地完全回退到上次commit

​	`git rebase`

先建立一个branch，`git branch x` `git rebase commit_id`
