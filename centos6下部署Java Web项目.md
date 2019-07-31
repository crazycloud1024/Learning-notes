---
title: centos6下部署Java Web项目
date: 2018-07-25 20:58:17
categories: Linux
tags: [Linux,Java]
---
**在云服务器上安装好系统后：**

**1.**先查看系统上有无原有的旧版本的mysql；tomcat；java。

![](http://upload-images.jianshu.io/upload_images/11830691-f6dfb82105b652dd?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

若有则先卸载；卸载命令使用
```
yum -y remove mysql*;
yum -y remove mysql*;
```
![](http://upload-images.jianshu.io/upload_images/11830691-43d808f091a46f1f?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](http://upload-images.jianshu.io/upload_images/11830691-ec95cd5f7262b443?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**2.**附上能下载的jdk1.7的链接，先下载jdk1.7后tomcat7放在本地目录下；

             #wget http://zhibo100.oss-cn-hangzhou.aliyuncs.com/software/jdk-7u79-linux-x64.tar.gz

![](http://upload-images.jianshu.io/upload_images/11830691-20b30a801774fa03?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

             #wget http://zhibo100.oss-cn-hangzhou.aliyuncs.com/software/apache-tomcat-7.0.59.tar.gz

![](http://upload-images.jianshu.io/upload_images/11830691-1846e005397d0a7f?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**3.**在/usr目录下建立一个java目录，java下建立jdk和tomcat目录用来存放解压好的文件；

![](http://upload-images.jianshu.io/upload_images/11830691-f63b1755bbb61beb?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**4.**解压jdk ，tomcat，命令如下；

            # tar -zxvf jdk-7u79-linux-x64.tar.gz -C /usr/java/

![](http://upload-images.jianshu.io/upload_images/11830691-0a4c5d248baf452d?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

            # tar -xzvf apache-tomcat-7.0.59.tar.gz -C /usr/java/tomcat/

![](http://upload-images.jianshu.io/upload_images/11830691-f0f055251f501663?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**5.**配置环境变量

配置JAVA环境变量并检测
 ```
vim /etc/profile
 ```
在文件末尾加上

#java

    JAVA_HOME=/usr/java/jdk1.7.0_79

    JRE_HOME=${JAVA_HOME}/jre

    CLASS_PATH=${JAVA_HOME}/lib

    PATH=$PATH:${JAVA_HOME}/bin

    export PATH JAVA_HOME CLASS_PATH PATH

保存退出

**6.**刷新环境变量
```
[root@luo ~]# source /etc/profile

[root@luo ~]# java -version

java version "1.7.0_79"

Java(TM) SE Runtime Environment (build 1.7.0_79-b15)

Java HotSpot(TM) 64-Bit Server VM (build 24.79-b02, mixed mode)
```
![](http://upload-images.jianshu.io/upload_images/11830691-17885fd9a976e687?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如图说明Java安装成功。

**7.**开启tomcat：
```
cd /usr/java/tomcat/apache-tomcat-7.0.59/bin/

./startup.sh
```
![](http://upload-images.jianshu.io/upload_images/11830691-4bc0af23b71f1818?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

验证能否访问，在浏览器下输入localhost：8080  能出现tomcat的页面就行了。

设置tomcat自启动，

![](http://upload-images.jianshu.io/upload_images/11830691-132c6530d79a13a2?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在进去  vim /etc/rc.d/rc.local 最下面加入：
```
export JDK_HOME=/usr/java/jdk1.7.0_79

export JAVA_HOME=/usr/java/jdk1.7.0_79

/usr/java/tomcat/apache-tomcat-7.0.59/bin/startup.sh
```
然后重启服务器试验。

**8.**安装mysql；因为yum源中默认没有mysql的安装源，所以需要手动添加，按照下面的语句顺序能顺利安装。

```
wget https://dev.mysql.com/get/mysql57-community-release-el6-9.noarch.rpm
```

b.安装用来配置mysql的yum源的rpm包

![](http://upload-images.jianshu.io/upload_images/11830691-2f80780e9aa910eb?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
rpm -Uvh mysql57-community-release-el6-9.noarch.rpm
或
yum localinstall -y mysql57-community-release-el6-9.noarch.rpm
```
安装成功后在/etc/yum.repos.d/下会多出几个mysql的yum源的配置

然后安装mysql

yum install mysql-community-server

![image](http://upload-images.jianshu.io/upload_images/11830691-357a3cb0da52fede?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

开启mysql服务
```
service mysqld start
```
mysql安装成功后创建的超级用户'root'@'localhost'的密码会被存储在**/var/log/mysqld.log**，可以使用如下命令查看密码
```
grep 'temporary password' /var/log/mysqld.log
```
![](http://upload-images.jianshu.io/upload_images/11830691-657e8f3558316810?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

由于mysql修改密码时候如果你输入简单密码会报错，将不能修改密码，使用初始密码更改MySQL检测密码的规则，代码如下：

首先，进入MySQL

![](http://upload-images.jianshu.io/upload_images/11830691-8e28c51ae237a245?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后修改validate_password_policy参数的值（可参考mysql报错博客）

```
mysql>setglobal validate_password_policy=0;

setglobal validate_password_length=1;

setglobal validate_password_mixed_case_count=2;
```

然后通过mysql安全设置

```
mysql_secure_installation修改密码

# mysql_secure_installation

    Enter current password for root (enter for none):　←　回车 

    Set root password? [Y/n]　←　回车 

    New password:　←　123456 

    Re-enter new password:　←　123456 

    Remove anonymous users? [Y/n]　←　回车(删除匿名用户) 

    Disallow root login remotely? [Y/n]　←　回车(禁止远程root登录) 

    Remove test database and access to it? [Y/n]　←　回车(删除test库) 

    Reload privilege tables now? [Y/n]　←　回车 

    Thanks for using MySQL! 

# mysql -u root -p 

    Enter password:123456 
```

设置远程登录：

    CREATE USER 'mysql'@'%' IDENTIFIED BY '123456';

    GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;

清除缓存：
`mysql> flush privileges;`

设置默认编码集

```
# vim /etc/my.cnf  

character-set-server=utf8

# /etc/init.d/mysqld restart  

# mysqld --version
```
最后把导出的war文件放到服务器中tomcat下的webapps目录下即可。
