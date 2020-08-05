## ubuntu 安装 python3
1. 安装依赖包：
```shell
sudo apt-get update
sudo apt-get install build-essential python-dev python-setuptools python-pip python-smbus
sudo apt-get install build-essential libncursesw5-dev libgdbm-dev libc6-dev
sudo apt-get install zlib1g-dev libsqlite3-dev tk-dev
sudo apt-get install libssl-dev openssl
sudo apt-get install libffi-dev
```
2. 下载安装包
安装到`/usr/local`目录
```shell
mkdir tmp
tar xpvf python3.7.0.tar.xz -C ./tmp
./configure --prefix=/usr/local
make
make install
```
3. 更改`/usr/bin/python`链接
```shell
ln -s /usr/local/bin/python3.7 /usr/bin/python37
ln -s /usr/local/bin/pip3.7 /usr/bin/pip37
```

