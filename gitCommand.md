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





