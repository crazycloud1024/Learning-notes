## vagrant国内ubuntu镜像

添加方式：

```shell
# ubuntu 18.04 LTS:
vagrant box add https://mirrors.tuna.tsinghua.edu.cn/ubuntu-cloud-images/bionic/current/bionic-server-cloudimg-amd64-vagrant.box --name ubuntu18

# ubunt 16.04 LTS：
vagrant box add https://mirrors.tuna.tsinghua.edu.cn/ubuntu-cloud-images/xenial/current/xenial-server-cloudimg-amd64-vagrant.box --name ubuntu16

# ubuntu14：
vagrant box add https://mirrors.tuna.tsinghua.edu.cn/ubuntu-cloud-images/vagrant/trusty/current/trusty-server-cloudimg-amd64-vagrant-disk1.box --name ubuntu14
```

初始化Vagrantfile:

```shell
vagrant init
# 修改
Vagrantfile：
...
config.vm.box = "ubuntu${}"
...

# 配置好后
vagrant up
```

