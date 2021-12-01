## `git`基本知识

> - #### `git`的框架
>
> > ```mermaid
> > graph LR
> > A[本地文件与分支] --> B[暂存区] --> C[本地仓库] --> |push| D[远程仓库]
> > D --> |pull| A
> > D --> |pull| C
> > 
>
> - #### 本地怎么能够`push`到远程
>
> > 1、本地的分支里面的文件与远程分支名字相同的文件，必须完全一致
> >
> > 2、本地可以新加文件，两地文件数量可以不一样
> >
> > 3、同样，文件名相同的文件不同也会导致`pull`失败
>
> - #### 本地创建新的分支
>
> > 1、如果使用`git branch branchname`创建的是有文件的分支
> >
> > 2、本地分支如果需要`pull`远程分支，可以创建无文件的分支

## `git`基本命令

> ```shell
> git init # 初始化git空间
> 
> # 从url的branchName上创建一个名为originName的远程主机
> git remote add <originName> -m <branchName> <url>    
> 
> # 复制远程仓库中的branchName分支到本地localBranch分支
> # 不加localBranch就是复制到当前HEAD分支
> git pull <originName> <branchName>[:<localBranch>]
> 
> # 从本地问价中添加名为fileName的文件到暂存区
> # 还可以使用 git *.txt 添加所有的txt文件
> git add <fileName>
> 
> # 将暂存区的文件添加上英文注释到本地仓库
> git commit -m "注释"
> 
> # 从originName主机将本地branchName1分支复制到远程branchName2分支
> # 不加的话，默认把当前HEAD分支push上去
> git push <originName> <branchName1>[:<branchName2>]
> 
> # 查看暂存区状态
> git status
> 
> # 查看当前主机状态
> git remote -v
> 
> # 查看版本信息
> git log
> 
> # 回退到上一个版本
> git reset --hard HEAD^
> 
> # 回退到版本号这个版本
> git reset hard <版本号>
> 
> # 创建分支，有文件，且继承于当前head分支
> git branch <分支名>
> 
> # 切换到另一个分支
> git checkout <分支名>
> ```
>
> 