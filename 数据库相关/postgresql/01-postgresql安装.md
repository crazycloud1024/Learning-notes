### centos7.6安装postgresql
##### 编译安装postgresql
```shell
# 安装编译需要的依赖包
yum install - y wget bison flex readline-devel zlib-devel
yum groupinstall "Development tools"

# 下载postgresql 最新的源码包
wget https://ftp.postgresql.org/pub/source/v11.5/postgresql-11.5.tar.gz

#解压进入文件夹内
tar -xzvf postgresql-11.5.tar.gz && cd postgresql-11.5

# 编译 指定安装目录和端口
./configure --prefix=/opt/pg11/ --with-pgport=5432

# 编译
gmake

# 安装
gmake install 

# 确定是否安装成功
/opt/pg11/bin/postgres --version

# 添加软连接 方便后期升级
ln -s /opt/pg11/  /opt/pgsql

# 创建一个非特权用户
groupadd -g 1000 postgres
useradd -g 1000 -u 1000 postgres
su - postgres
```
##### yum 安装postgresql

```shell
# 添加yum源
yum install https://mirrors.cloud.tencent.com/postgresql/repos/yum/11/redhat/rhel-7.6-x86_64/pgdg-centos11-11-2.noarch.rpm

# 安装包
yum install -y postgresql11-server postgresql11-contrib
```