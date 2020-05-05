# Git笔记  

## 1. 概念  

Git是分布式管理仓库，而SVN是集中式（CVS）管理的代表。CVS是保存文件修改的历史记录（文件变更的列表），Git是记录文件及其修改的快照并存储相应索引。

## 2. 命令  

### 2.1 安装配置  

#### 2.1.1 安装好Git后先全局配置用户信息：用户名、邮箱。如果在某个项目中需要使用其他用户，可以在项目根目录下去掉'global'单独配置。

``` shell  
git config --global user.name "John Doe"
git config --global user.email johndoe@example.com
```  

#### 2.1.2 检查配置信息  

如果想要检查你的配置，可以使用 git config --list 命令来列出所有 Git 当时能找到的配置  

``` shell  
git config --list
```  

你可能会看到重复的变量名，因为 Git 会从不同的文件中读取同一个配置（例如：/etc/gitconfig 与 ~/.gitconfig）。 这种情况下，Git 会使用它找到的每一个变量的最后一个配置。

你可以通过输入 git config \<key\>： 来检查 Git 的某一项配置
可以根据key修改以上的值：git config \<key\> \<value\>  

## 1. 把本地代码推到远程仓库  

1.1 上github新建仓库，不选新建README，懒得再解决冲突  
1.2 （先进入项目文件夹）通过命令 git init 把这个目录变成git可以管理的仓库，记得创建.gitignore文件忽略上传某些文件。  

``` shell  
git init  
```  

1.3 把文件添加到版本库中，使用命令 git add .添加到暂存区里面去，不要忘记后面的小数点“.”，意为添加文件夹下的所有文件  

``` shell  
git add .  
```  

1.4 用命令 git commit告诉Git，把文件提交到仓库。引号内为提交说明  

``` shell  
git commit -m 'first commit'  
```  

1.5 关联到远程库  

``` shell  
git remote add origin 你的远程库地址  
```  

1.6 获取远程库与本地同步合并（如果远程库不为空必须做这一步，否则后面的提交会失败）  

``` shell  
git pull --rebase origin master  
```  

1.7 把本地库的内容推送到远程，使用 git push命令，实际上是把当前分支master推送到远程。执行此命令后会要求输入用户名、密码，验证通过后即开始上传。  

``` shell  
git push -u origin master  
```  
