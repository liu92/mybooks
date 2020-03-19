## GitBook 使用

## 安装

 1、``node -v  ``

   命令查看 是否按照 node.js ,命令行输入上述命令。出现node版本号则已经安装，否则无须安装。

 2、``https://nodejs.org/en/ ``

​      进入node 官网 下载node.js进行安装

 3、``npm install gitbook-cli  -g``

​     安装gitbook, 命令行输入上述命令

 4、``gitbook -V``

  查看node.js是否安装成功，出现node版本号则说明安装成功，否则没有, 注意V大写

## 使用

1、``gitbook init`` 初始书籍目录

2、``gitbook serve`` 编译和预览书籍

3、``http://localhost:4000``



## 测试

1、在电脑中创建一个文件夹，如下图

![](G:\Github\mybooks\frist_book\image\image-20200309214611394.png)

2、在这个目录执行cmd 命令然后安装 node。 使用命令``npm install gitbook-cli  -g``

3、然后使用  ``gitbook -V``  查看node

4、进入 一个目录然后初始化  ``gitbook init``  中间只有一个空格，这个过程比较慢 。执行如下

![](G:\Github\mybooks\frist_book\image\image-20200309221407226.png)

5、在初始后会生成README.md和SUMMARY.md文件 执行 ``gitbook serve`` , 这个相当于是启动的服务。

![](G:\Github\mybooks\frist_book\image\image-20200309221631621.png)



6、访问localhost:4000

![](G:\Github\mybooks\frist_book\image\image-20200309221731365.png)

7、如果要访问自己的 文档，那么修改SUMMARY.md 文件。如下[]() 这样输入要加入的文档

![](G:\Github\mybooks\frist_book\image\image-20200309222105883.png)

![](G:\Github\mybooks\frist_book\image\image-20200309222316053.png)

这个添加的文档和这个SUMMARY文档在同一目录下

![](G:\Github\mybooks\frist_book\image\image-20200309222347645.png)