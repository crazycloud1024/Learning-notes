---
title: Linux基础-权限管理
date: 2017-03-28 09:11:00
categories: Linux
tags: [Linux]
---
# Linux基础-权限管理

### 一.权限管理

#### 1.用户权限：

**认证：1.1 Authentication；授权：Authorization；审计：Adition**

#### 1.2 Linux用户和组的主要配置文件：
- /etc/passwd：用户及其属性信息（名称、UID、主组ID等）
- /etc/group：组及其属性信息
- /etc/shadow：用户密码及其相关属性
- /etc/gshadow：组密码及其相关属性

#### 1.3 安全上下文：进程代表用户在进行：谁启动了进程，进程就拥有谁的身份（以进程（process）发起者的身份运行）。
- root:/bin/cat
- mage:/bin/cat

#### 1.4 用户
##### 1.4.1 用户User：
- 令牌token,identity
- Linux用户：Username/UID
- 用户级别：
    - 管理员：root, 0
    - 普通用户：1-65535
        - 系统用户：1-499, 1-999 （CentOS7）
            - 对守护进程获取资源进行权限分配
        - 登录用户:500+, 1000+（CentOS7）
            - 交互式登录

##### 1.4.2 passwd文件格式：
- login name：登录用名（wang）
- passwd：密码  (x)
- UID：用户身份编号 (1000)
- GID：登录默认所在组编号  (1000)
- GECOS：用户全名或注释
- home directory：用户主目录 (/home/wang)
- shell：用户默认使用shell (/bin/bash)

    ```shell
    tcpdump:x:72:72::/:/sbin/nologin
    vampire:x:500:500:vampire:/home/vampire:/bin/bash
    mysql:x:27:27:MySQL Server:/var/lib/mysql:/bin/false
    ```
##### 1.4.3 shadow文件格式：
- 登录用名
- 用户密码:一般用sha512加密
- 从1970年1月1日起到密码最近一次被更改的时间
- 密码再过几天可以被变更（0表示随时可被变更）
- 密码再过几天必须被变更（99999表示永不过期）
- 密码过期前几天系统提醒用户（默认为一周）
- 密码过期几天后帐号会被锁定
- 从1970年1月1日算起，多少天后帐号失效
    ```
    tcpdump:!!:17735::::::
    vampire:$6$yk7XOpZX0ZngOz6D$qVI/.Q7Ch7M.KRfjsPC.    WTiN9w2qJgUofENaFlnLxXpvzIN/lICVwcIme1b3LHMixqXxnEIGkgepuUIpsoadU1:1773 5:0:99999:7:::
    mysql:!!:17737::::::
    ```

##### 1.4.4 用户创建
语法：`useradd [options] LOGIN`
- -u UID 
- -o 配合-u 选项，不检查UID的唯一性
- -g GID：指明用户所属基本组，可为组名，也可以GID
- -c "COMMENT"：用户的注释信息
- -d HOME_DIR: 以指定的路径(不存在)为家目录
- -s SHELL: 指明用户的默认shell程序
    ​    可用列表在/etc/shells文件中
- -G GROUP1[,GROUP2,...]：为用户指明附加组，组须事先存在
- -N 不创建私用组做主组，使用users组做主组
- -r: 创建系统用户 CentOS 6: ID<500，CentOS 7: ID<1000
- -m  创建家目录，用于系统用户
- -M  不创建家目录，用于非系统用户

    ```shell
    [root@localhost ~]# useradd -u 111 luoqian  -c "vamp" -s /bin/csh -m
    [root@localhost ~]# tail -1 /etc/passwd
    luoqian:x:111:501:vamp:/home/luqoian:/bin/csh
    ```

##### 1.4.5 用户属性的修改：

语法：`usermod [OPTION] login
- -u UID: 新UID
- -g GID: 新主组
- -G GROUP1[,GROUP2,...[,GROUPN]]]：新附加组，原来的附加组将会被覆盖；若保留原有，则要同时使用-a选项
- -s SHELL：新的默认SHELL
- -c 'COMMENT'：新的注释信息
- -d HOME: 新家目录不会自动创建；若要创建新家目录并移动原家数据，同时使用-m选项
- -l login_name: 新的名字；
- -L: lock指定用户,在/etc/shadow 密码栏的增加 ! 
- -U: unlock指定用户,将 /etc/shadow 密码栏的 ! 拿掉
- -e YYYY-MM-DD: 指明用户账号过期日期
- -f INACTIVE: 设定非活动期限

##### 1.4.6 删除用户：
语法：`userdel [OPTION]... login`
- -r: 删除用户家目录

##### 1.4.7 切换用户或以其他用户身份执行命令
语法：`su [options...] [-] [user [args...]]`
- 切换用户的方式：
	- su UserName：非登录式切换，即不会读取目标用户的配置文件，不改变当前工作目录
	- su - UserName：登录式切换，会读取目标用户的配置文件，切换至家目录，完全切换
- root su至其他用户无须密码；非root用户切换时需要密码
- 换个身份执行命令：
	`su [-] UserName -c 'COMMAND'`
- 选项：`-l  --login`
   ​    `su -l UserName  相当于 su - UserName`

##### 1.4.8 修改密码
语法：`passwd [OPTIONS] UserName: `修改指定用户的密码
- 常用选项：
    - -d:删除指定用户密码
    - -l:锁定指定用户
    - -u:解锁指定用户
    - -e:强制用户下次登录修改密码
    - -f: 强制操作
    - -n mindays: 指定最短使用期限
    - -x maxdays：最大使用期限
    - -w warndays：提前多少天开始警告
    - -i inactivedays：非活动期限
    - --stdin：从标准输入接收用户密码
       echo "PASSWORD" | passwd --stdin USERNAME

#### 1.5 组

##### 1.5.1 Linux组的类别
- 用户的主要组（primary group）
    - 用户必须属于一个且只有一个主组
    - 组名同用户名，且仅包含一个用户，私有组
- 用户的附加组（supplementary group）
    - 一个用户可以属于零个或多个附加组

##### 1.5.2 组group：
- Linux组：Groupname/GID
- 管理员组：root, 0
- 普通组：
    - 系统组：1-499, 1-999（CENTOS7）
    - 普通组：500+, 1000+（CENTOS7）

##### 1.5.3 group文件格式
- 群组名称：就是群组名称
- 群组密码：通常不需要设定，密码是被记录在 /etc/gshadow 
- GID：就是群组的 ID 
- 以当前组为附加组的用户列表(分隔符为逗号)
    ```shell
        [root@localhost ~]# cat /etc/group
        root:x:0:
        bin:x:1:bin,daemon
        daemon:x:2:bin,daemon
    ```

##### 1.5.4 gshadow文件格式
- 群组名称：就是群组名称
- 群组密码：
- 组管理员列表：组管理员的列表，更改组密码和成员
- 以当前组为附加组的用户列表：(分隔符为逗号)
  
    ```shell
    [root@localhost ~]# cat /etc/gshadow
    root:::
    bin:::bin,daemon
    daemon:::bin,daemon
    sys:::bin,adm

    ```

##### 1.5.5 创建组：
语法：`groupadd [OPTION]... group_name`
- -g GID: 指明GID号；[GID_MIN, GID_MAX]
- -r: 创建系统组
    - CentOS 6: ID<500
    - CentOS 7: ID<1000

##### 1.5.6 修改和删除组：
- 组属性修改：groupmod
语法：groupmod [OPTION]... group
```shell
	-n group_name: 新名字
	-g GID: 新的GID
```

- 组删除：groupdel
    `groupdel GROUP`

##### 1.5.7 更改组密码
```shell
    组密码：gpasswd
    gpasswd [OPTION] GROUP
	    -a user  将user添加至指定组中
	    -d user  从指定组中移除用户user
	    -A user1,user2,...  设置有管理权限的用户列表
    newgrp命令：临时切换主组
            如果用户本不属于此组，则需要组密码
```

#### 2 文件权限：

##### 2.1 文件的权限主要针对三类对进行定义
- owner: 属主, u，文档所有者
- group: 属组, g，文档所属组用户
- other: 其他, o，其他用户
- 每个文件针对每类访问者都义了三种权限
    - r: Readable：读
    - w: Writable：写
    - x: eXcutable：执行

##### 2.2 文件和目录权限的区别：
- 文件：
    - r: 可使用文件查看类工具获取其内容
    - w: 可修改其内容
    - x: 可以把此文件提请内核启动为一个进程
- 目录：
    -  r: 可以使用ls查看此目录中文件列表
    -  w: 可在此目录中创建文件，也可删除此目录中的文件
    -  x: 可以使用ls -l查看此目录中文件列表，可以cd进入此目录

##### 2.3 修改文件权限：
```shell
chmod [OPTION]... OCTAL-MODE FILE...
      -R: 递归修改权限（改目录的时候想修改目录内部所有文件的权限，但是不建议）
chmod [OPTION]... MODE[,MODE]... FILE...
      MODE：
      修改一类用户的所有权限：
	    u=  g= o= ug=  a= u=,g=
      修改一类用户某位或某些位权限
	    u+  u- g+ g- o+ o- a+ a- + -
chmod [OPTION]... --reference=RFILE FILE...
	（referance）参考RFILE文件的权限，将FILE的修改为同RFILE
```

##### 2.4 修改文件的属组和属主：
- 修改文件的属主：chown
    - chown [OPTION]... [OWNER]\[:[GROUP]] FILE...
    - 用法：
	    - OWNER
	    - OWNER:GROUP
	    - :GROUP
        - 命令中的冒号可用.替换
        - -R: 递归
- 语法：`-chown [OPTION]... --reference=RFILE FILE...`
- 修改文件的属组：chgrp
    ```shell
     chgrp [OPTION]... GROUP FILE...
     chgrp [OPTION]... --reference=RFILE FILE...
     -R 递归
    ```

> **注意：**
    - (权限一定要给全，不然会从左边补全，例如如果给77，则默认为077）
    - (目录一定要有执行权限，普通文件不能随便给执行权限（防止一些恶意文件留后门）)，
    - (只写+w,只有属主加写权限)

##### 2.5 新建文件和目录的默认权限

- umask值 可以用来保留在创建文件权限
- 新建FILE权限: 666-umask
    - 如果所得结果某位存在执行（奇数）权限，则将其权限+1
		新建DIR权限: 777-umask	
- 非特权用户umask是 002
- root的umask 是 022
- umask: 查看
- umask #: 设定
    - umask 002
- umask –S 模式方式显示
- umask –p 输出可被调用
- 全局设置： /etc/bashrc 用户设置：~/.bashrc

##### 2.6 访问控制列表
- ACL：Access Control List，实现灵活的权限管理
- 除了文件的所有者，所属组和其它人，可以对更多的用户设置权限 
- CentOS7 默认创建的xfs和ext4文件系统具有ACL功能
- CentOS7 之前版本，默认手工创建的ext4文件系统无ACL功能,需手动增加
    - tune2fs –o acl /dev/sdb1
    - mount –o acl /dev/sdb1  /mnt/test
- ACL生效顺序：所有者，自定义用户，自定义组，其他人

- 为多用户或者组的文件和目录赋予访问权限rwx
    - mount -o acl /directory
    - getfacl  file |directory
    - setfacl  -m  u:wang:rwx file|directory
    - setfacl  -Rm g:sales:rwX directory 
    - setfacl  -M  file.acl file|directory
    - setfacl  -m  g:salesgroup:rw file| directory
    - setfacl  -m  d:u:wang:rx  directory 
    - setfacl  -x  u:wang  file |directory
    - setfacl  -X file.acl  directory
    ```shell
    [root@localhost ~]# getfacl linux222.txt
    # file: linux222.txt
    # owner: root
    # group: root 
    user::rw-
    group::r--
    other::r--

    [root@localhost ~]# setfacl -m u:vampire:wx linux222.txt
    [root@localhost ~]# getfacl linux222.txt
    # file: linux222.txt
    # owner: root
    # group: root
    user::rw-
    user:vampire:-wx
    group::r--
    mask::rwx
    other::r--

    ```
