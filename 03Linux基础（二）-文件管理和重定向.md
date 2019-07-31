---
title: Linux基础-文件管理和重定向
date: 2017-04-15 15:18:00
categories: Linux
tags: [Linux]
---
# Linux基础-文件管理和重定向

##### 1. 每一个分区都是一个独立存在的文件系统

##### 2. 目录：路径映射符

##### 3. 文件
文件有两种数据
- 元数据：描述数据的数据属性 metadata
- 数据：data

FHS：文件系统层级结构标准

##### 4. Linux下的文件类型
- -：普通文件
- d：目录文件
- b：块设备
- c：字符设备
- l：符号链接文件
- p：管道文件pip，FIFO（first in，first out）
- s：套接字文件：服务加IP端口，作为一个服务的入口。socket

##### 5. 显示当前路径
- 每个shell和系统进程都有一个当前的工作目录 
- CWD:current work directory
- 显示当前shell CWD的绝对路径
- pwd: printing working directory
    - -P 显示真实物理路径
    - -L 显示链接路径（默认）

##### 6. 绝对路径和相对路径

**绝对路径:**
- 以正斜杠开始
- 完整的文件的位置路径
- 可用于任何想指定一个文件名的时候

**相对路径名:**
- 不以斜线开始
- 指定相对于当前工作目录或某目录的位置
- 可以作为一个简短的形式指定一个文件名

**注：**
- 基名:basename
- 目录名:dirname

##### 7. cd命令

**cd  改变目录**
- 使用绝对或相对路径：
    - cd /home/wang/       
    - cd home/wang
- 切换至父目录 ：cd ..
- 切换至当前用户主目录： cd
- 切换至以前的工作目录： cd -
- 选项：-P 
- 相关的环境变量：
    - 显示环境变量：printenv
        - PWD：当前目录路径 
        - OLDPWD：上一次目录路径
- ~ ：当前用户家目录
- ~luo ：用户luo家目录
- ~+ ：当前工作目录
- ~- ：前一个工作目录

##### 8. 显示目录

- 列出当前目录的内容或指定目录(显示的数据都是元数据)
- 用法：ls [options]\[files_or_dirs]
- 示例:
    - ls -a ：包含隐藏文件
    - ls -l ：显示额外的信息
    - ls -R ：目录递归显示
    - ls -ld ： 目录和符号链接信息
    - ls -1 ：文件分行显示
    - ls –S ：按从大到小排序
    - ls –t ： 按mtime排序
    - ls –u ：配合-t选项，显示并按atime从新到旧排序
    - ls –U : 按目录存放顺序显示
    - ls –X : 按文件后缀排序

##### 9. 查看文件状态:显示当前文件的时间戳

 **命令：stat**
- 文件：metadata, data
- 三个时间戳：
    - access time：访问时间，atime，读取文件内容
    - modify time: 修改时间, mtime，改变文件内容（数据）
    - change time: 改变时间, ctime，元数据发生改变

**注意：
元数据发生变化，数据不一定变化
数据发生变化，元数据一定变化**

##### 10. 文件通配符（对文件名做通配）(glob、globing、wildcard)
- \* ：匹配零个或多个字符
- ? ：匹配任何单个字符
- [0-9] : 匹配数字范围
- [a-z] ：字母
- [A-Z] ：字母
- [wang] : 匹配列表中的任何的一个字符
- [^wang] : 匹配列表中的所有字符以外的字符
- 预定义的字符类：（帮助：man 7 glob）
    - [:digit:]：任意数字，相当于0-9
    - [:lower:]：任意小写字母
    - [:upper:]：任意大写字母
    - [:alpha:]：任意大小写字母
    - [:alnum:]：任意数字或字母
    - [:blank:]：水平空白字符
    - [:space:]：水平或垂直空白字符
    - [:punct:]：标点符号
    - [:print:]：可打印字符 
    - [:cntrl:]：控制（非打印）字符 
    - [:graph:]：图形字符 
    - [:xdigit:]：十六进制字符

##### 11. touch命令：

`touch [OPTION]... FILE...`
- -a   仅改变 atime和ctime
- -m  仅改变 mtime和ctime
- -t  [[CC]YY]MMDDhhmm[.ss]  
指定atime和mtime的时间戳
- -c  如果文件不存在，则不予创建
> 例如：`touch -m -t 201806300000.00 /etc/passwd`

```
    [root@localhost ~]# stat /etc/passwd
    File: `/etc/passwd'
    Size: 1677      	Blocks: 8          IO Block: 4096           regular file
    Device: 803h/2051d	Inode: 787752      Links: 1
    Access: (0644/-rw-r--r--)  Uid: (   0/   root)  Gid: (0/  root)
    Access: 2017-04-10 18:57:44.311586312 +0800
    Modify: 2017-04-10 18:48:32.693647980 +0800
    Change: 2017-04-10 18:48:32.693647980 +0800
    [root@localhost ~]# touch -m -t 201703300000.00 /etc/passwd
    [root@localhost ~]# stat /etc/passwd
    File: `/etc/passwd'
    Size: 1677      	Blocks: 8          IO Block: 4096   regular file
    Device: 803h/2051d	Inode: 787752      Links: 1
    Access: (0644/-rw-r--r--)  Uid: (  0/  root) Gid: ( 0/    root)
    Access: 2017-04-10 18:57:44.311586312 +0800
    Modify: 2017-03-30 00:00:00.000000000 +0800
    Change: 2017-04-10 14:13:22.439425983 +0800
```

##### 12. 复制：所谓的复制就是创建一个新的空文件，然后在将源文件的数  据流读出来，然后写进去。

cp默认不复制目录下的内容，选项：-r：递归复制
```shell
1.cp [OPTION]... [-T] SOURCE DEST
2.cp [OPTION]... SOURCE... DIRECTORY
3.cp [OPTION]... -t DIRECTORY SOURCE...（2.3等同）
4.cp SRC DEST
```
- SRC是文件：
     - 如果目标不存在：新建DEST，并将SRC中内容填充至DEST中
     - 如果目标存在：
         - 如果DEST是文件：将SRC中的内容覆盖至DEST中。
         	​    基于安全，建议为cp命令使用-i选项。
        - 如果DEST是目录：在DEST下新建与原文件同名的文件，并    将SRC中内容填充至新文件中。

- cp SRC... DEST
   - SRC...：多个文件
     - DEST必须存在，且为目录，其它情形均会出错；

- cp SRC DEST
    - SRC是目录：此时使用选项：-r
        - 如果DEST不存在：则创建指定目录，复制SRC目录中所有文件  至DEST中；
        - 如果DEST存在：
            - 如果DEST是文件：报错
            - 如果DEST是目录

- cp的常用选项
    ```
    -i：覆盖前提示	 –n:不覆盖，注意两者顺序
    -r, -R: 递归复制目录及内部的所有内容
    -a: 归档，相当于-dR --preserv=all
    -d：--no-dereference --preserv=links 不复制件，只  复制 链接名
    --preserv[=ATTR_LIST]  （元数据信息）
        mode: 权限
        ownership: 属主、属组
        timestamp: （时间戳（atime、mtime不变）（cti会变））
        links：链接属性
        xattr：拓展属性
        context：保持selinux的安全属性 
        all：以上所有
    -p: 等同--preserv=mode,ownership,timestamp
    -v: --verbose 显示提示信息
    -f: --force 强制
    -u:--update 只复制源比目标更新文件或目标不存在的文件
    --backup=numbered 目标存在，覆盖前先备份加数字后缀
    ```

cp命令中：具体的流程是：

分配一个空闲的inode号，在inode表中生成新条目，在目录中创建一个目录项，将名称inode编号关联
拷贝数据生成新的文件。


##### 13. 移动和重命名文件

```
mv [OPTION]... [-T] SOURCE DEST
mv [OPTION]... SOURCE... DIRECTORY
mv [OPTION]... -t DIRECTORY SOURCE...
```
    常用选项： 
    -i: 交互式
    -f: 强制

mv操作时，具体的流程是：

如果mv命令的目标和源在相同的文件系统，作为mv 命令
用新的文件名创建对应新的目录项，删除旧目录条目对应的旧的文件名，不影响inode（除时间戳）或磁盘上的数据位置：没有数据被移动！

如果目标和源在一个不同的文件系统， mv相当于cp和rm。

##### 14. 删除

`rm [OPTION]... FILE...`

常用选项：
- -i: 交互式
- -f: 强制删除
- -r: 递归
- --no-preserve-root : 强制删除/目录，忽略root  
示例： 
	rm -rf    /

rm操作时候，具体的逻辑是：

- 链接数递减，从而释放的inode号可以被重用
- 把数据块放在空闲列表中
- 删除目录项
- 数据实际上不会马上被删除，但当另一个文件使用数据块时将被覆盖。

##### 15. 目录操作

tree 显示目录树
- -d: 只显示类型为目录
- -L level：指定显示的层级数目
- -P ：pattern 只显示由指定pattern匹配到的路径
```
[root@localhost ~]# tree -P *Base* /etc/yum.repos.d/
/etc/yum.repos.d/
└── CentOS-Base.repo
```

mkdir 创建目录
- -p: 存在时不报错，且可自动创建所需的各目录
- -v: 显示详细信息
- -m MODE: 创建目录时直接指定权限
```
[root@localhost ~]# mkdir -p -v -m 777 /tmp/c
mkdir: created directory `/tmp/c'
[root@localhost ~]#
```

rmdir 删除空目录
- -p: 递归删除父空目录
- -v: 显示详细信息
- rm -r 递归删除目录树
```java{.line-numbers}
[root@localhost ~]# mkdir -p -v /tmp/x/y/z
mkdir: created directory `/tmp/x'
mkdir: created directory `/tmp/x/y'
mkdir: created directory `/tmp/x/y/z'
[root@localhost ~]# rmdir -p -v /tmp/x/y/z
rmdir: removing directory, `/tmp/x/y/z'
rmdir: removing directory, `/tmp/x/y'
rmdir: removing directory, `/tmp/x'
rmdir: removing directory, `/tmp'
rmdir: failed to remove directory `/tmp': Directory not     empty
```

##### 16. 索引节点  

inode（index node）表中包含文件系统所有文件列表
一个节点 （索引节点）是在一个表项(inode table)，包含有关文件的信息（ 元数据 包括：

- 文件类型，权限，UID，GID
- 链接数（指向这个文件名路径名称个数）
- 该文件的大小和不同的时间戳
- 指向磁盘上文件的数据块指针
- 有关文件的其他数据

![inode结构表](https://upload-images.jianshu.io/upload_images/11830691-b45e402408acf428.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![inode存值形式](https://upload-images.jianshu.io/upload_images/11830691-056ec985b755059a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![根据inode找文件步骤](https://upload-images.jianshu.io/upload_images/11830691-1a24908fb5a0b703.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

文件引用一个是 inode号

人是通过文件名来引用一个文件

一个目录是目录下的文件名和文件inode号之间的映射

##### 17. 硬链接和软链接

详细说明[https://www.ibm.com/developerworks/cn/linux/l-cn-hardandsymb-links/inde.html#listing1(https://www.ibm.com/developerworks/cn/linux/l-cn-hardandsymb-links/inde.html#listing1 "转载自IBM博客")
​    
 **1. 硬链接**
- 创建硬链接会增加额外的记录项以引用文件
- 对应于同一文件系统上一个物理文件
- **每个目录引用相同的inode号**
- 创建时链接数递增
- 删除文件时：
    - rm命令递减计数的链接
    - 文件要存在，至少有一个链接数
    - 当链接数为零时，该文件被删除
- 不能跨越驱动器或分区
- 语法: `ln filename  [linkname ]`
> 注意： 目录不能使用硬链接

**2. 软链接**
- 一个符号链接指向另一个文件
- ls - l的 显示链接的名称和引用的文件
- 一个符号链接的内容是它引用文件的名称
- 可以对目录进行
- 可以跨分区
- **指向的是另一个文件的路径；其大小为指向的路径字符串的长度；不增  - 加或减目标文件inode的引用计数；**
- 语法：`ln -s  filename  ​​[linkname]`
>注意:加粗部分为硬链接和软链接的核心区别。


##### 18. 确定文件内容

文件可以包含多种类型的数据
检查文件的类型，然后确定适当的打开命令或应用程序使用
​    
语法：`file [options] <filename>...`
​    
常用选项:
- -b 列出文件辨识结果时，不显示文件名称
- -f filelist 列出文件filelist中文件名的文件类型
- -F 使用指定分隔符号替换输出文件名后默认的”:”分隔符
- -L 查看对应软链接对应文件的文件类型
- --help 显示命令在线帮助

##### 19. 标准输入与输出

- 程序：指令+数据
    - 读入数据：Input
    - 输出数据：Output
- 打开的文件都有一个fd: file descriptor (文件描述符)
- Linux给程序提供三种I/O设备
    - 标准输入（STDIN）－0　默认接受来自键盘的输入
    - 标准输出（STDOUT）－1　默认输出到终端窗口
    - 标准错误（STDERR）－2　默认输出到终端窗口
- I/O重定向：改变默认位置

- 输入重定向：
    ```
    [root@localhost ~]# cat > /tmp/luoq.txt <<EOF
    > woshishui
    > EOF
    [root@localhost ~]# cat /tmp/luoq.txt
    woshishui
    ```

##### 20. 把输出和错误重新定向到文件

- STDOUT和STDERR可以被重定向到文件
- 命令　操作符号　文件名
- 支持的操作符号包括：
    - \> 　把STDOUT重定向到文件
    - 2>　把STDERR重定向到文件
    - &>　把所有输出重定向到文件
```
[root@localhost ~]# touch /tmp/error.txt;cat /tmp/luo.txt1 2>/tmp/error.txt
[root@localhost ~]# cat /tmp/error.txt
cat: /tmp/luo.txt1: No such file or directory
[root@localhost ~]# 
[root@localhost ~]# cat /tmp/luoq.txt &> /tmp/error.txt
[root@localhost ~]# cat /tmp/error.txt
woshishui
[root@localhost ~]# cat /tmp/luoq.txt11 &>  /tmp/error.txt
[root@localhost ~]# cat /tmp/error.txt
cat: /tmp/luoq.txt11: No such file or directory
```
- \> 文件内容会被覆盖
    - set –C  禁止将内容覆盖已有文件,但可追加
    - \>| file 强制覆盖
    - set +C  允许覆盖
- \>>  原有内容基础上，追加内容
- 2>	覆盖重定向错误输出数据流
- 2>>	 追加重定向错误输出数据流
- 标准输出和错误输出各自定向至不同位置 `COMMAND > /path/to/file.out 2>/path/to/error.out`
- 合并标准输出和错误输出为同一个数据流进行重定向
    - &>   覆盖重定向
    - &>>  追加重定向
    - COMMAND > /path/to/file.out 2>&1 （顺序很重要）
    - COMMAND >> /path/to/file.out 2>&1
    -  ()：合并多个程序的STDOUT
    - 	( cal 2007 ; cal 2008 ) > all.

##### 21. tr命令

**tr  转换和删除字符**
​    
- 语法:`tr [OPTION]... SET1 [SET2]`
- 选项：
    - -c –C --complement：取字符集的补集
    - -d --delete：删除所有属于第一字符集的字符 
    - -s --squeeze-repeats：把连续重复的字符以单独一个字符表示
    - -t --truncate-set1：将第一个字符集对应字符转化为第二字符集对应的字符
    - [:alnum:]：字母和数字 [:alpha:]：字母 [:cntrl:]：控制（非打印）字符 [:digit:]：数字 [:graph:]：图形字符 [:lower:]：小写字母 [:print:]：可打印字符 [:punct:]：标点符号 [:space:]：空白字符 [:upper:]：大写字母 [:xdigit:]：十六进制字符
> 例如：`tr ‘a-z’   ‘A-Z’< /etc/issue`

##### 22. 管道

- 管道（使用符号“|”表示）用来连接命令
- 命令1 | 命令2 | 命令3 | …
    - 将命令1的STDOUT发送给命令2的STDIN，命令2的STDOUT送到命令3的STDIN
    - STDERR默认不能通过管道转发，可利用2>&1 或 |& 实现
    - 最后一个命令会在当前shell进程的子shell进程中执行来
    - 组合多种工具的功能
    - ls  | tr 'a-z' 'A-Z'

- 有些命令不支持管道传输过来的数据，可以加上 - 执行

##### 23. tee命令，一路输入两路输出

- 命令1 | tee　[-a ] 文件名 | 命令2 
    - 把命令1的STDOUT保存在文件中，做为命令2的输入
    - -a 追加
- 使用：
    - 保存不同阶段的输出
    - 复杂管道的故障排除
    - 同时查看和记录输出
> 例如：`[root@localhost ~]# cat /etc/passwd | tr -d 'abc' | tr 'd-z' 'D-Z' | tee /tmp/luo.txt`

##### 24. selinux：安全Linux策略

- 配置文件在/etc/selinux/config ：
- 查看状态的命令：getenforce  
	- 三种状态：
		- enforcing：强制：违反规则不能运行
	    - permissive许可：可以允许运行，但是会记录，用来后期的审计
		- disabled：禁用：关闭selinux

##### 25. 历史命令：history的环境变量配置：

**HISTCONTROL**
- -ignoredups（忽略重复）
- -ignorespace（忽略空格）	
- -ignoreboth（忽略所有)

##### 26.权限
认证；授权：审计 Authentication；Authorization；Adition
