## git 基本使用和原理



### 集中式SVN:

   svn因为每次存存的都是差异 需要的硬盘空间相对的小一点  可是回滚的速度很慢

   优点：代码存放在单一的服务器上，便于项目的管理

   缺点：单一的服务器 集中化，如果服务器宕机那么 你写的代码就不能得到保障， 并且本地代码拉取的是最新代码，但是没有历史版本，所以如果服务器出现问题 那么就没有历史版本，并且不能进行回滚到历史版本。

### 一、分布式Git:

   git每次存的都是项目的完整快照 需要的硬盘空间相对大一点（Git 团队对代码做了机制的压缩，最终需要的实际空间比svn多不了太多， 并且Git的回滚速度极快）

  优点：分布式版本控制管理项目 ，存放的不是项目版本和版本之间的差异，它存的是索引，可以每一个客户端可以放下整个项目的历史记录， 相当于每一个客户端 就是一个服务器 ，并且客户端记录每个版本的历史记录。

  

##### 1.git使用：

 

```
#查看版本 git --version
h@h-PC MINGW64 /e/workspace/learn-java-concurrenty (master)
$ git --version
git version 2.24.0.windows.2
# 查看配置 git config --list
h@h-PC MINGW64 /e/workspace/learn-java-concurrenty (master)
$ git config --list
http.sslcainfo=D:/programfiles/Git/mingw64/ssl/certs/ca-bundle.crt
http.sslbackend=openssl
diff.astextplain.textconv=astextplain
core.autocrlf=true
core.fscache=true
core.symlinks=false
user.name=liu92
user.email=470437289@qq.com
core.bare=false
core.repositoryformatversion=0
core.filemode=false
core.symlinks=false
core.ignorecase=true
core.logallrefupdates=true
remote.origin.url=https://github.com/liu92/learn-java-concurrenty.git
remote.origin.fetch=+refs/heads/*:refs/remotes/origin/*
gui.wmstate=normal
gui.geometry=841x483+100+100 189 218
```

#####  2.初始化

 

```
# 配置 
/etc/gitconfig 文件：系统中对所有用户都普遍适用的配置。若使用git config 时用 --system 选项，
读写的就是这个文件。
~/.gitconfig 文件：用户目录下的配置文件只适用于该用户。若使用gitconfig 时使用 -- global 选项，
读写的就是这个文件。
.git/config 文件：当前项目的Git目录中配置文件（也就是工作目录中的.git/config文件）这里的配置仅仅
针对当前项目有效 （什么都没有添写  就是每个项目去填写配置信息）
用户信息：
$ git config --global user.name "liu"
$ git config --global user.email liu@example.com
#查看配置 git config --list
```

##### 3.底层命令 基础linux命令：

 

```shell
#初始化仓库
git init
clear：清除屏幕
echo 'test content': 往控制台输出信息 并且可以 创建一个文件  比如： echo 'test' > text.txt
ll: 将当前目录下的子文件 & 子目录平铺在控制台
find 目录名：将对应目录下的子孙文件&子孙目录平铺在控制台
find 目录名 -type f: 将对应目录下的文件平铺在控制台
rm 文件名： 删除文件
mv 源文件  重命名文件：重命名
cat 文件的url: 查看对应文件的内容
vim 文件的url(在英文模式下)
   按 i进入插入模式  进行文件的编辑
   按 esc 键& 按:键 进行命令的执行
           !q 强制退出（不保存）
           wq 保存退出
           set nu 设置行号
```

设置 行号：如下 使用vim 打开文件，然后按esc 键 ， 然后输入： set nu 就可以看到文本中有行号了

再次按i 进行插入就可以看到 文本中行号的变化了

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/c05a56a9-aee9-4d45-a37f-d9a5e5dacd48.jpg)

git 文件目录

在一个工作空间中 使用git bash here 然后初始化 仓库，初始化后可以看到 有.git 隐藏文件

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/ee0a0326-2bd7-4a4f-9ad2-c5e127307f69.png) 

 

```shell
# hooks:   目录包含客户端端或服务端的钩子脚本；(类似回调函数)
# info:    包含一个全局排除文件
# logs:    保存日志信息
# objects:  目录存储所有数据内容
# refs:     目录存储指向数据 的提交对象的指针(分支)
# config:   文件包含项目特有的配置选项
# description:   用来显示对仓库的描述信息
# HEAD:          文件指示目前被检出的分支
# index:   文件保存暂存区信息
```

区域：

 

```
#工作区(沙箱环境)： 本地代码目录
#暂存区：
#版本区：
```

##### 4、对象

$\textcolor{Red}{Git对象}$：只存内容, git对象代表文件的一次次版本

$\textcolor{Red}{树对象}$：暂存区的快照， 树对象代表项目的一次次版本

$\textcolor{Red}{提交对象}$: 项目的版本就是一个提交对象，本质上项目的快照 应该是一个树对象

 

```shell
一、Git对象：
  Git的核心部分是一个简单的键值对数据库。你可以向该数据库输入任意类型的内容，
 它会返回一个键值，通过该键值可以在任意时候再次检索giant内容
 #1.向数据库写入内容，并返回对应键值
   命令： 见下面测试
   echo 'test content' | git hash-object -w  --stdin
      - w 选项指示 hash-object 命令存储数据对象；若不指定此选项，则该命令仅返回对应的键值
      --stdin(standard input) 选项则指示该命令从标准输入读取内容；若不指定此选项,
        则须在命令尾部给出待存储文件的路径
     git hash-object -w 文件路径
         存文件
     git hash-object 文件路径
       返回对应文件的键值
    返回：
      该命令输出一个长度为40 该字符的校验和。这是一个SHA-1哈希值
#2.查看Git 是如何存储数据的
  命令：
    find .git/objects - type f
  返回  
   h@h-PC MINGW64 /e/BaiduNetdiskDownload/test (master)
   $ find .git/objects/ -type f
  .git/objects/d6/70460b4b4aece5915caf5c68d12f560a9fe3e4
#3. 根据键值拉取数据
  命令：
   git cat-file -p d670460b4b4aece5915caf5c68d12f560a9fe3e4
   -p 选项可指示该命令自动判断内容的类型，并为我们显示格式友好的内容
   -t 选项 表示存储的类型
   返回： 对应文件的内容
   # 示例 
   h@h-PC MINGW64 /e/BaiduNetdiskDownload/test (master)
   $ git cat-file -p d670460b4b4aece5915caf5c68d12f560a9fe3e4
   test content
#4. 对一个文件进行简单的版本控制
  # 4.1 创建一个新文件并将其内容存入数据库
   命令：
     echo 'version 1' > test.txt
     git hash-object -w test.text
   返回hash值：560a3d89bf36ea10794402f6664740c284d4ae3b
   
  # 示例
  h@h-PC MINGW64 /e/BaiduNetdiskDownload/test (master)
  $ echo 'test.txt v1' > test.txt
  h@h-PC MINGW64 /e/BaiduNetdiskDownload/test (master)
  $ git hash-object -w ./test.txt
  warning: LF will be replaced by CRLF in ./test.txt.
  The file will have its original line endings in your working directory
  560a3d89bf36ea10794402f6664740c284d4ae3b
 # 4.2 向文件写入新内容，并再次将其存入数据库
   命令：
    echo 'version 2' > test.txt
    git hash-object -w test.text
  
# 问题 
  1. 记住文件的每一个版本所对应的 SHA-1 值并不现实
  2、在Git 中，文件名并没有被保存---我们仅保存了文件的内容
   解决方案:数对象
# 注意
   当前的操作都是在本地数据库进行操作的 不涉及暂存区（暂存区代表版本库）
   
   
二、树对象：
  它能解决文件名保存的问题，也允许我们将多个文件组织到一起。Git 以一种类似于UNIX文件系统的方式存储内容。
 所有内容均以树对象和数据对象（git对象）的形式存储，其中树对象对应了UNIX中的目录项，数据对象（git对象）
 则大致上对应文件内容。一个树对象包含了一条或多条记录（每条记录含有一个指向git 对象或者子树对象的SHA-1
 指针，以及相应的模式、模型、文件名信息）。一个树对象也可以包含另一个对象。
 
 #查看暂存区当前的样子
   git ls-file -s
 
#1.查看树对象
   命令：
     git cat-file -p master^{tree} (或者是树对象的hash)
     master^{tree} 语法表示 master 分支上最新的提交所指向的树对象。
#2.构建树对象
   我们可以通过update-index; write-tree; read-tree 命令来构建树对象并塞入到暂存区。
   *操作：   
      1、利用update-index命令 为test.txt文件的首个版本--创建一个暂存区。 并通过write-tre命令生成树对象。
      命令：
         git update-index--add --cacheinfo 100644\
         
         git write-tree
         文件模式   100644,表明这是一个普通文件
                   100755，表示一个可执行文件；
                   120000，表示一个符号链接。
         --add选项：
             因为此前该文件并不在暂存区中，首次需要 --add
         --cacheinfo选项：
            因为将要添加的文件位于Git数据库中，而不是位于当前目录下所以需要 --cacheinfo
      2.新增nex.txt，将new.txt和 test.txt文件的第二个版本塞入暂存区。并通过write-tree命令生成树对象。
         命令：
           echo 'new file' > new.txt
           git update-index --cacheinfo 100644\
           
           git update-index --add nex.txt
           git write-tree
      3.将一个树对象加入第二个树对象，使其成为新的树对象
       命令：
           git read-tree   
               --prefix=bak 06e21bb0105e2de6c846725a9a7172f57dd1af96（第一课树的hash）
           git write-tre
              read-tree命令，可以把树对象读入暂存区
               
      
      4. 暂存区内容写到数据库里面去的时候(git的objects中数据存在中去) 暂存区会被清空      
      
      5.在修改了test.txt内容后需要重新为test.txt 生成树对象
      $ git hash-object -w test.txt
      fe14f86d920174fe9be2ea43eff03605447c95ca
      
三、提交对象： 树对象才是一次项目版本的快照，提交对象只是对这个进行了封装，封装给了一下注释信息和作者xinx
    我们可以通过调用commit tree 命令创建一个提交对象，为此需要指定一个树对象的SHA-1值，以及该提交的父
    提交对象（如果有的化 第一次将暂存区做快照就没有父对象）
# 1.创建提交对象
     echo 'frist commit' | git commit-tree d8239f
     返回：
        fdf4....
# 2.查看提交对象
     git cat-file -p fdf4....
     
     
```

测试： 如果不指定文件路径 那么就要加上 --stdin , 产生的hash值是唯一的

并且这个数据不会存到git 中 object中去，如果加上了 -w 那么这个数据就会存导 git 目录中 object文件中去

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/18d7d06b-c58c-4ea5-be40-6e9f79f359db.png)

测试

 

```
$ git update-index --add --cacheinfo 100644 560a3d89bf36ea10794402f6664740c284d4ae3b test.txt
```

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/2cc30786-1e46-4b35-90a5-31a8f91f52c9.png)

查看暂存区 git ls-files-s

并且git write-tree 生成一个hash, 这个hash对应的类型是tree  

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/714556a1-c07a-47e8-acea-92ba7276c354.png)

新增加文件:  并为为这个文件生成git对象

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/b3273c49-c7fb-47ac-bfc5-e16fc9d2afe7.png)

 

```
h@h-PC MINGW64 /e/BaiduNetdiskDownload/test (master)
$ find .git/objects/ -type f
.git/objects/06/e21bb0105e2de6c846725a9a7172f57dd1af96  这个test  项目的第一个版本(树对象)
.git/objects/56/0a3d89bf36ea10794402f6664740c284d4ae3b  test.txt 文件的第一个版本（git对象）
.git/objects/ea/e614245cc5faa121ed130b4eba7f9afbcc7cd9  test.txt 文件的第二个版本（git对象）
.git/objects/fe/14f86d920174fe9be2ea43eff03605447c95ca  nex.txt 文件的第一个版本（git对象）
```

.

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/59051d21-7011-43ce-81de-e6bd6f193553.png)

覆盖原来的 test.txt, 并且将 new.txt 加入到暂存区， 这个时候还没有生成树对象

，当前暂存区中目录和工作区的目录是一样的 能作为项目的第二个版本

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/4d78263d-ae09-4102-9958-f702debb267c.png)

生成树对象，那么这个时候版本库会再生成一个对象。 这个几个对象（几千个对象）代码项目的下一个版本

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/f6599acd-ea01-4306-a0fe-baaa8f957ae8.png)

### 二、高层命令：

##### 1.git add  命令流程 并不是工作目录直接到暂存区， 而是工作目录先到版本库 ，版本库再到暂存区

下面示例： 通过git add 命令 后  可以看到这个 版本库会有一个hash，然后暂存区也会有 

 

```
h@h-PC MINGW64 /e/BaiduNetdiskDownload/test
$ git init
Initialized empty Git repository in E:/BaiduNetdiskDownload/test/.git/
h@h-PC MINGW64 /e/BaiduNetdiskDownload/test (master)
$ echo 'test' > tt.txt
h@h-PC MINGW64 /e/BaiduNetdiskDownload/test (master)
$ git ls-files -s
h@h-PC MINGW64 /e/BaiduNetdiskDownload/test (master)
$ find .git/objects/
.git/objects/
.git/objects/info
.git/objects/pack
h@h-PC MINGW64 /e/BaiduNetdiskDownload/test (master)
$ git add ./  
warning: LF will be replaced by CRLF in tt.txt.
The file will have its original line endings in your working directory
h@h-PC MINGW64 /e/BaiduNetdiskDownload/test (master)
$ find .git/objects/
.git/objects/
.git/objects/9d
.git/objects/9d/aeafb9864cf43055ae93beb0afd6c7d144bfa4
.git/objects/info
.git/objects/pack
h@h-PC MINGW64 /e/BaiduNetdiskDownload/test (master)
$ git ls-files -s
100644 9daeafb9864cf43055ae93beb0afd6c7d144bfa4 0       tt.txt
h@h-PC MINGW64 /e/BaiduNetdiskDownload/test (master)
$ git cat-file -p 9daeafb
test
h@h-PC MINGW64 /e/BaiduNetdiskDownload/test (master)
$ git cat-file -t 9daeafb
blob
```

现在只有版本库只有一个git对象， 树对象和提交对象都没有生成，其实我们可以查看项目空间，这个文件

已经创建但是 还没有加到 工作区。

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/4dbc243d-2c58-4e16-bd2f-06667d966f30.png)

##### 2.使用 git commit  -m "注释"

这个命令会生成 树对象和 提交对象

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/490168cf-5a80-4051-8e25-cc44f8bb5e3d.png)

文件图标变化

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/2527bba8-3d13-43be-9961-c7ba33361d67.png)

##### 3、基本操作

 

```
1、跟踪新文件（暂存）
  命令：git add 文件名
  作用：跟踪一个新文件
      再次运行git status命令，会看到README文件已被跟踪，并处于暂存状态
2、暂存已修改
  现在README文件都已暂存，下次提交时就会一并记录到仓库
  
3、查看已暂存和未暂存的更新
   实际上git status 的显示比较简单，仅仅是列出了修改过的文件，如果要查看具体修改了什么地方，可以用 git diff 命令。这个命令
   已经解决我们两个问题：当前做的那些更新还没有暂存？有哪些更新已经暂存起来准备好了下次提交？
     . 当前做的那些更新还没有暂存？
       命令： git diff 
     . 有哪些更新已经暂存起来准备好了下次提交？
       命令： git diff --cached 或者 git diff --staged(1.6以上)
4.提交更新      
   git commit -m "注释"
5. 跳过使用暂存区域
    尽管使用暂存区的方式可以精心准备要提交的细节，但是有时候这么做略显繁琐。Git提供了一个跳过使用暂存区域的方式，只要在提交的时候，
    给git commit 加上-a选项， Git 就会自动把所有一级跟踪过的文件暂存起来一并提交，从而跳过git add 步骤
      git commit -a
6. 移除文件
   rm 移除命令 
   要 从git中移除某个文件，就必须要从已跟踪文件清单中注册删除（确切地说，是在暂存区域注册删除），然后提交。可以用git rm命令完成
   此项工作，并连带从工作目录中删除指定的文件，这样以后就不会出现未跟踪文件清单了。
    工作目录里面做了删除操作，版本库中其实做了 新增操作，新增了 树对象 和一个提交对象。 不是真正的删除， 只是工作空间里面没有了。
    要找回来 回到上次提交就可以了。
    git rm 命令 相当于 几步操作  首先在工作区将文件删除，然后执行 git add ./ 命令 将修改提交到暂存区
7. 文件改名修改
  mv 源文件名  重命名
  同样 
  git mv 很上面的git rm命令一样 它包含几个操作
8. 查看日志
 git log
 git log --pretty= online  显示在一行
 git log --online
```

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/fb639546-c527-4fa1-a7f8-332bd99bdde0.png)

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/4be48618-dfbc-4853-8622-e6fa72027cc3.png)


删除操作示例:

工作目录里面做了删除操作，版本库中其实做了 新增操作，新增了 树对象 和一个提交对象。 不是真正的删除， 只是工作空间里面没有了。

要找回来 回到上次提交就可以了。

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/f34b0575-4369-4d92-98b0-037fff0a5a4d.png)

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/dcb4bd40-a955-49c7-b0fb-5c10d9a69753.png)

##### 4、Git 分支操作

 

```
# 创建分支
# git branch  '分支名'
lin@DESKTOP-RKM9KV2 MINGW64 /i/te/books (master)
$ git branch dd
lin@DESKTOP-RKM9KV2 MINGW64 /i/te/books (master)
$ git log --oneline
da72ddf (HEAD -> master, dd) delete
b1ae45b ggg
eda0324 del li.txt
9c84769 哈哈哈
# 要想指针指向 新建的分支那么就切换分支
# git checkout dd
lin@DESKTOP-RKM9KV2 MINGW64 /i/te/books (master)
$ git checkout dd
Switched to branch 'dd'
lin@DESKTOP-RKM9KV2 MINGW64 /i/te/books (dd)
$ git log --oneline
da72ddf (HEAD -> dd, master) delete
b1ae45b ggg
eda0324 del li.txt
9c84769 哈哈哈
# 如果不写分支名就是展示分支
h@h-PC MINGW64 /e/BaiduNetdiskDownload/test (hep)
$ git branch
* hep
  master
#查看项目分叉历史
# git log --oneline --decorate --graph --all 但是这个命令太长了所以可以配置别名
示例：
git config --global alias.co "log --oneline --decorate --graph --all" 
再次输入的时 使用 git co 就可以查看历史分叉记录了
# 配置别名
Git 并不会在你输入部分命令时自动推断你想要的命令。如果不想每次输入完成的Git命令，
可以通过git config 文件来轻松地为每一个命令设置一个别名
# git config --global alias.co(别名)   checkout(原命令 需要配置的命令)
# git config --global alias.br branch
# git config --global alias.ci commit
# git config --global alias.st status  注意单独这个单词 status 不加双引号
# 查看分支的最后一次提交
#git branch -v
h@h-PC MINGW64 /e/BaiduNetdiskDownload/test (master)
$ git branch -v
* master fe7ae25 delete
# 新建一个分支并且使分支执行对应的提交对象
# git branch name commitHash
$ git branch ssss 685a925
# 注意切换分支的时候先查看下状态
#git statu  如果看到下面的显示后才说明是正确的
h@h-PC MINGW64 /e/BaiduNetdiskDownload/book (master)
$ git status
On branch master
nothing to commit, working tree clean
```

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/67678d82-c72f-4854-8b22-de14706db205.png)

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/ffd4dc1b-8ceb-4898-b9b3-436e13568e2c.png)

切换分支后 添加新的文件，然后 git add ./ 添加到暂存区， 然后git commit 

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/4ca0dab6-845b-4b8e-8aeb-2996fa0f724d.png)

建立分支，但是不切换

$ git branch ssss 685a925

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/9b9ddc1d-db8a-4489-8248-d5c0acba0401.png)

别名

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/1d1e4a86-2532-47c1-9a15-cca3888dd832.png)

##### 5.切换分支会动那些地方

 

```
#切换分支
  最佳实践 ：每次切换分支前 当前分支一定得是干净的（已提交状态）
  坑：在切换分支时，当前分支如果有未暂存的修改 或者 有未提交的暂存 ，分支可以切换，但是这种
     操作可能污染其他的分支
#1.移动HEAD
#2.暂存区
#3.工作目录
git te(别名) 查看分支记录
gti ls-files -s 查看暂存区
并且可以看到切换分支时 HEAD指针会移动
```

示例图

![img](https://github.com/liu92/mybooks/frist_book/image/gitimage/b009df7a-a03b-4e47-88df-7017d6b394ff.png)

##### 5.1、如果切换时候 当前分支上面有未提交的文件那么 切换到其他分支上去就会 污染其他分支

可以看到当前在test分支上 添加了c.txt 文件，如果这个没有被 git add 

git commit -m  那么切换分支时就会造成问题   并且这个时候文件还没有被加入到暂存区中

要使用 git add 才将文件加入到暂存区

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/1030a2d3-66e2-47d5-8302-28f807344d58.png)

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/c787855e-bd23-4bee-bb21-aadfb80d5b89.png)

这是 分支 test 目录写的文件结构

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/7546f4c4-a592-446f-a6d8-d8544b9833d4.png)

切换到主分支 ，可以看到暂存区只有 test.txt文件

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/feb787a9-b852-420d-be40-f3c764bcad58.png)

工作目录也是

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/6d50377e-ec43-4e6e-8b03-bc173b1aaa8a.jpg)

##### 5.2 如果在分支test 上去修改 c.txt 文件，这个时候再去切换分支 就不能切换了

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/6a9e6589-9447-4c3c-9da5-2bc57235a39f.png)

##### 5.3 示例 修改 问题 "#53"

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/a3a1123e-fbaa-4460-a24b-54b0368533b2.png)

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/6aee50d6-407f-45f8-adb1-1d63d5706cc1.png)

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/5fde61c3-991f-4900-b5eb-d65a683f926f.png)

##### 5.4 当遇到紧急问题是我们切换到主分支，并且新切换一个分支去解决紧急问题。

这个时候HEAD 指向新的分支 ，一定要以主分支为基点进行分支的切换。不能在当前开发的分支上去

创建新的分支 因为当前分支可能 的问题没有解决 从而造成其他的问题。

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/38a9e61d-adce-4b62-9f58-90927cc2df98.png)

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/5c0915f1-980e-4d4b-b1f9-33a797303a24.png)

##### 5.5 合并分支 

  使用主分支合并 紧急分支 所以要先查看 分支状态，然后切换到主分支。

下面示例图 可以看到 在没有合并时的图形，使用 git merge 后 meergency 分支后结果。

但是这个 iss53 文件就过期了，因为 master分支上 已经在 它前面去了。iss53分支还是原来主分支的内容

但是没有emergency 分支的内容。  如果在emergency分支上修改了 test.txt 文件，并且合并到主分支后

这时候切换到iss53 分支继续修改问题，可是这个时候也有可能 修改到 同一个文件这样 就会造成冲突。

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/a70fbff5-2be7-413c-8a18-64cbb0ace534.png)

比如下面切换到 iss53 分支，这个时候会修改到和 紧急分支修改到相同的文件这样就会造成了冲突

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/d10ea682-5c8a-4610-8039-59bc5e9dd747.png)

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/6cdbdabd-c964-46a0-8ebc-f09c7314215b.png)

##### 5.6 切换到主分支 进行合并，就会看到有冲突

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/34b129a8-2140-4e42-a928-9f57ee80b522.png)

查看冲突文件

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/8c87486c-c622-45d1-a013-bb85e51cc911.jpg)

解决冲突文件 保留两个分支提交的内容

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/ef1a0c55-9382-44e4-a23e-e10e23863e40.jpg)

查看状态

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/fdeecb4f-cf6f-42a5-81c5-9f1662da4ef6.png)

再次使用命令 git add  就表示解决了冲突了 ，然提交 后就可以看到 分支变化了

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/3dc368c9-ed1c-4790-8286-b88b4cc21410.png)

查看分支图，可以看到已经到主线上来了，然后删除 分支，从图中可以看出记录

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/9f7de16d-dcae-4c2d-a2a8-099e22cddd28.png)

##### 6、Git 存储

 

```
    有时，当你在项目的一部分上已经工作一段时间后，所要东西都进入混乱的状态，而这时你想要切换到另一个分支做一点别的事情。问题是，你不想仅仅因为过会儿回到这一点而为做了一半的工作创建一次提交。那么针对这个问题的答案是git stash 命令
git stash 命令会将未完成的修改保存到一个栈上，而你可以在任何时候重新应用这些改的（git stash apply）
git stash list:查看存储
git stash app stash@{}
         如果不指定一个存储，Git认为指定的是最近的存储
git stash pop  来应用存储然后立即从栈上扔掉它
git stash drop 加上将要移除的存储名字来移除它
```

如下图 这个时候修改了文件 ，如果再次切换就会提交 有修改。这个时候不想提交那么就 将其保存到栈上去

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/6171e3d3-047e-4561-a12f-5a54518791ba.png)

查看git stash list ，首次查询时没有，这里进行存储。然后可以看到栈里面已经有了一份

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/e4cd6523-5823-43a5-bcc8-9107e6392fbf.png)

再次查看git status 状态 ，已经是没有可以提交的了。其实 git stash 也是做了提交，但是它的提交不会记录到log里面，git 会给你想看的东西。git默认做的提交 是看不到的

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/4d0192e7-5cb5-426f-ab41-b18edd1e924a.png)

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/71aa6320-9c93-4f9a-86e7-4a6c6719beaf.png)

这个时候就可以切换分支了

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/78130385-53c9-4110-b9a6-60a4007a9466.png)

当要 回到那个分支去做未完成的事情时 切换回去，然后使用git stash apply 将其取出来，然后查看已经取出来了，并状态也是未提交

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/2e44b47c-65b2-40a8-8df9-94bc0bd2e4e0.png)

再 将分支取出来是 栈里面还有这个分支信息，那么我们要将其删除

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/7006f204-6855-47d9-b46a-6a64c64c5214.png)

##### 7、撤回和重置

 

```
工作区：
   如何撤回自己工作目录的修改（如果还没有加入到工作区那么 就不能用git进行管理）
   git restore <file>
暂存区：
   如何撤回自己的暂存
版本库：
   如何撤回自己的提交
   
 完整操作查看
 git reflog
```

##### 7.1工作区撤回：下面 的文件 被修改了，但是还没有存入暂存区， 所以使用 git restore 撤回工作目录的修改。

使用命令后 可以看到文件 已经被撤回到上一次的内容了

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/da1c358c-5bb8-4b02-8948-1ad82478344b.png)

##### 7.2暂存区撤回：将一个文件修改后 存在暂存区，如果要撤回

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/bda3fde9-cb26-4b06-a322-419a8db2bd7b.png)

使用命令 git restore --staged da.txt 进行撤回，这个命令 就是取消暂存

可以看到 暂存区的 内容已经撤销回来了

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/a7302b93-e70a-4a83-993b-fa6cd6990f70.png)

##### 7.3 将版本库的内容撤回，如果是内容写错了，直接修改重新提交就可以了

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/f1d96758-2397-4a13-b7a4-35df364dda66.png)

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/0e6c432c-17a2-4e46-90bf-3b9a54f156d6.png)

如果是注释写错了，那么就是要 git commit  --amend 进行修改同时可以看 log 日志已经修改了

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/a24305f0-fe06-4d69-9535-19ae8d0180f6.png)

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/15550500-2e7b-48ae-90ca-a1de7742d38f.png)

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/3c9933c7-2003-4e51-90a6-81196daf7925.png)

当修改了文件 ，然后加入到暂存后 ，再次修改

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/e4fbec05-225d-4903-874f-cf773247c2b3.png)

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/c2e6db6f-ec66-4f8f-afaa-aeecf6c85799.png)

##### 8、reset 命令

 

```
1. 移动HEAD
     reset 做的第一件事就是移动HEAD的指向   
第一部： git reset --soft HEAD~ (--amend) 
        只动HEAD (带着分支一起移动)  而checkout 不一样 不会带着分支移动
第二部：git reset [--mixed] HEAD~     
       动HEAD (带着分支一起移动) 并且动了暂存区
       
第三部： git rest --hard HEAD~       
       动了HEAD (带着分支一起移动)
       动了暂存区
       动了工作目录
## checkout 
  git checkout commit hash & git rest --hard commithash
      1. checkout 只懂HEAD  --hard动HEAD 而且带着分支一起走
      2. checout 对工作目录是安全的   --hard是强制覆盖工作目录
    git checkout commithash  
    git checkout --filename
      相比于git reset --hard commithash  --filename
      第一步 第二步都没做
      只会动工作目录
     git checkout commithash <file>
      将会跳过第1步
      更新暂存区
      更新工作目录
      
## 路径reset
  git reset [--mixed] HEAD filename  (reset 将会跳过第1步)
  动暂存区
  
## 注意 
 git  checkout [branch] 与运行git reset --hard [branch]非常相似，它会更新三种 使其看起来像[brranch],不过有两点重要的区别
    首先不同于reset --hard ,  checkout对于工作目录是安全的，它会通过检查来确保不会将已更改的文件弄的。而reset --hard 则不会做检查
    就全面替换所有东西
    第二个重要的区别是如何更新HEAD。reset会移动HEAD分支的指向，而checkout只会移动HEAD自身来指向另一个分支。
```

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/155e3fc5-b6d6-4520-953c-495c0ad45752.png)

是要 git reset --sofe HEAD~ 命令 可以看到 这个暂存区 和文件 没有变化

这个命令只动 head

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/7b5066dc-e8fb-4c28-abfc-2da75226fb60.png)

如果这时要回到 最开始 的指向那么 还是使用 git reset --soft hash  就可以回到 上一次提交

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/d9e3eaa3-9a16-4547-9997-6df7aee529a2.png)

使用命令 git reset --mixed HEAD~

暂存区 文件查看值 是 1 ，2

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/d9ba8263-d1c0-488a-94ba-a98cfecbf694.png)

如果要会去那么 使用 git reset --mixed 7fd831d

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/41c26db0-7997-42a5-acdb-f1ed442cc224.png)

使用 git reset --hard HEAD~

![img](https://github.com/liu92/mybooks/tree/master/frist_book/image/gitimage/6138931a-55e8-4f70-926a-250fbc97d97e.png)

##### 9.数据恢复

 

```
git log --pretty=oneline 查看日志
```

##### 10、打tag 

 

```
#列出标签
git tag 
#创建标签
git tag v1.0
#查看特定标签
git show <tagname>
#删除标签
git tag -d <tagname>
#远程标签
#检出标签
如果想查看某个标签所指向的文件版本，可以使用git checkout命令
当回退到对应的tag时，会产生头部分离的情况
git checkout <tagname>
git checkout -b version   使用这个命令 可以在tag切换出一个分支
```

甚至还有人丧心病狂地把`lg`配置成了：

 

```
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```

 

### 三、git操作基本流程

```
git操作基本流程：
 创建工作目录 对工作目录进行修改
 git add ./    将修改添加到暂存区
   git add 命令相当于底层命令： 
    #1.git hash-object -w (文件名，修改了多少文件 此命令就要被执行多少次)
    #2.git update-index 
  
  git add 先去生成一个git对象 ，至于生成少个git对象那么就要看 你工作目录修改了多少文件。该一个文件一个git对象
  然后先放到版本库里面去，再其次内部通过 update-index命令 将git对象和文件做一一对应 在暂存区做记录。
  
  
 git status     查看状态 当查看时修改的文件显示 绿颜色表示已经被暂存，红颜色表示被修改但是还没有 暂存
 git commit -m ""注释内容   将暂存区提交到版本库
   相当于：
    git write-tree
    git commit-tree
```

 

```
 git commit -m ""注释内容   将暂存区提交到版本库git 高层命令
git init    初始化仓库
git status  查看文件的状态
git diff    查看那些修改还没有暂存
git diff --staged  查看那些修改了以及被暂存了 还没提交
git long  --oneline 查看提交的历史记录
git add  ./   将修改添加到暂存区
git rm  文件名    删除工作目录中对应的文件，再将修改添加到暂存区
git mv  原文件名  新文件名  将工作目录中的文件进行重命名，再将修改添加到暂存区
git commit
git commit -a  跳过暂存区
git commit -a -m 注释
git commit -m ""注释内容   将暂存区提交到版本库
git branch  '分支名'  添加分支 （如果不加分支名那么这里就是展示分支）
git branch -d name "分支名" 删除分支
git branch -d name "分支名"  强制删除分支
```

### 四、总结

 

```shell
底层命令：
git 对象
  git hash-object -w fileUrl: 生成一个key(hash值)：val(压缩后的文件内容)键值对存到.git/objects
tree 对象
  git update-index -add -cacheinfo 100644 hash test.txt： 往暂存区添加一条记录（让git对象 对应
  上文件名） 存到.git/index
  git write-tree: 生成树对象存到.git/objects
commit 对象
  echo 'first commit'| git commit-tree treehash: 生成一个提交对象存到 .git/objects
  
 对以上对象的查询
   git cat-file -p hash  :拿对应对象的内容
   git cat-file -t hash  :拿对应对象的类型

#查看暂存区
  git ls-files -s
  
  
# -----------------------------------------------
# 高层命令
# 安装 
 查看版本 git version
 
#初始化配置
 
 git config --global user.name 'ln'
 git config --global user.email ll@example.com
 git config --list

# 初始化仓库 
git init 

#C新增
在工作目录中新增文件
git status
git add 
git commit -m 'message'

#U(更新)
在工作目录中修改文件
git status
git add  
git commit -m 'message'

##D(删除文件 & 重命名)
在工作目录中删除文件
git rm  要删除文件               git mv 老文件名  新文件名
git status                      git status
git commit -m 'message'         git commit -m 'message'

###R(查询)
git status    :(查看工作目录中文件的状态(已跟踪(已提交，已暂存，已修改), 未跟踪))
git diff      :查看未暂存的修改
git diff --cache : 查看未提交的暂存  
git log --oneline  ： 查看提交记录


## 分支
 分支的本质其实就是一个提交对象的可变指针 ， 在.gt/refs/heads 中 存放的是一个对象所要提交对的hash值 
 HEAD: 
   是一个指针 它默认指向master分支 切换分支时其实就是让HEAD指向不同的分支 
   每次有新的提交时 HEAD都会带着当前指向的分支 一起往前移动
git branch ：查看分支列表
git branch -v：查看分支指向的最新提的提交
git branch name:  在当前提交对象上创建新的分支

git branch name commitHash(版本穿梭 ，时光机) : 在指定的提交对象上创建新的分支

git checkout name : 切换分支
git checkout -b 分支名：  这个创建分支并且切换到新创建的分支上去
git branch -d name:  删除空的分支， 删除已经被合并的分支
git branch -d name "分支名"  强制删除分支
git log --oneline --decorate --graph -all ：查看整个项目的分支图


#合并分支
git merge branchname
   快进合并 不会产生冲突（在同一个分支）
   典型合并 有机会产生冲突（在不同的分支）
   解决冲突 打开冲突的文件 进行修改  然后  add commit 
   

查看分支列表：git branch
查看合并到当前分支的 分支 ： git branch --merged
  一旦出现在这个列表 就应该删除
查看没有合并到当前分支的分支列表： git branch --no-merged
  一旦出现在这个列表中 就应该观察一下 是否需要合并
  
# git分支的注意点
  在切换的时候 一定要保证当前分支是干净的！！！
   允许切换分支：
       分支上所有的内容处于 已提交状态
       （避免）分支上的内容是初始化创建 处于未跟踪状态
       （避免）分支上的内容是初始化创建 处于已暂存暂存
   不允许切换：
     分支上所有的内容处于 已修改状态 或 第二次以后的已暂存状态
     
   在分支的工作做到一半时，如果有切换分支的需求，我们应该将现有的工作存储起来
     git stash: 会将当前分支上的工作推到一个栈中
     分支切换 进行其他工作 完成其他工作后 切回原分支
     git stash apply： 将栈顶的工作内容还原 但不让任何内容出栈
     git stash drop :  取出栈顶的工作内容后 就应该将其删除（出栈）
     git stash pop  :  是 git stash apply + git stash drop  集合
     git stash list :  查看存储
     
     
#撤销 & 重置
  撤销工作目录的修改 ： git checkout --filename
  撤销暂存区的修改：    git reset  HEAD  filename
  撤销提交：           git commit --amend
  
  
# reset 三部曲

   git reset --soft HEAD(commithash)~ ---> 用commithash 的内容重置HEAD 内容
   
   git reset [--mixed](中括号代表可以省略) HEAD~  --->   用commithash 的内容重置HEAD 内容 重置暂存区
   
   git rest --hard HEAD~     --->   用commithash 的内容重置HEAD 内容 重置暂存区  重置工作目录
    

## 路径reset
   所有的路径reset 都要省略第一步！！！
     第一步是重置HEAD内容 我们知道HEAD 本质指向一个分支 ，分支的本质是一个 提交对象 
     提交对象 指向一个树对象，树对象又有可能指向多个git对象 一个git 对象代表一个文件！！！
     HEAD可以代表一系列文件的状态！！！
  git reset [--mixed] HEAD filename  (reset 将会跳过第1步)
     用commithash 中filename的内容重置暂存区
  
## checkout 深入理解
  git checkout commit hash & git rest --hard commithash 很想
      1. checkout 只动HEAD  --hard动HEAD 而且带着分支一起走
      2. checout 对工作目录是安全的   --hard是强制覆盖工作目录
    git checkout commithash  
    git checkout --filename
      相比于git reset --hard commithash  --filename
      第一步 第二步都没做
      只会动工作目录
     git checkout commithash <file>
      将会跳过第1步
      更新暂存区
      更新工作目录
      
      

  
## 注意 
 git  checkout [branch] 与运行git reset --hard [branch]非常相似，它会更新三种 使其看起来像[brranch],
   不过有两点重要的区别
    首先不同于reset --hard ,  checkout对于工作目录是安全的，它会通过检查来确保不会将已更改的文件弄的。
    而reset --hard 则不会做检查  就全面替换所有东西
    第二个重要的区别是如何更新HEAD。reset会移动HEAD分支的指向，而checkout只会移动HEAD自身来指向另一个分支。
    
   共同点：
     都需要重置HEAD 暂存区 工作目录 
   区别： 
      checkout对于工作目录是安全的                 reset --hard 是强制覆盖
      checout 动HEAD时不会带着分支走而是在切换分支  reset --hard时是带着分支走
      
  checout + 路径
     git checkout commithash filename
         重置暂存区
         重置工作目录
     git checkout --filename
         重置工作目录
    
```