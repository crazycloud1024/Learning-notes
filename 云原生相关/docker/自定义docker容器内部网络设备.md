#### 自定义docker容器内部网络设备

在宿主机上创建网络设备br0，分配IP地址 192.168.1.254，并启动该网络设备。

```shell
# 添加网桥
$ sudo brctl addr br0
# 设置ip
$ sudo ip addr add 192.168.1.254/24 dev br0
# 启动网桥
$ sudo ip link set dev br0 up
```

创建一个veth对foo、bar，并将foo连接到网桥br0上，如下所示：

```shell
# 创建veth网卡对
$ sudo ip link add foo type veth peer name bar
# 安装bridge-utils，使用brctl命令将一端foo连接到网桥上
$ sudo brctl addif br0 foo
# 启动该端设备
$ sudo ip link set foo up
# 检查网桥设备是否已经插入网卡对的一端
$ brctl show
```

然后使用`--net=none`的方式启动容器，会创建一个网络命名空间，但是该容器内部只有一个回环网卡。如果需要对容器的网络进行配置（例如增加一个网络接口，设置路由信息）。首先就要找到网络命名空间ID,保存在`/var/run/docker/netns`下面，而linux的默认的命名空间保存在`/var/run`下，所以需要进行以下操作

```shell
# 链接默认命名空间
$ sudo ln -s /var/run/docker/netns /var/run/netns
# 找到该容器ID
$ NID=$(sudo ip netns)
```

将veth设备的另一端bar放入到容器的命名空间中，并通过`ip netns exec`进去该命名空间设置网络名称和MAC地址，在容器内部创建一个网络接口，并为其分配IP地址192.168.1.10

```shell
# 将bar加入到该命名空间中
$ sudo ip link set bar netns $NID
# 设置名字
$ sudo ip netns exec $NID ip link set dev bar name eth1
# 设置MAC地址
$ sudo ip netns exec $NID ip link set eth1 address 12:34:56:78:9a:bc
# 启动网卡
$ sudo ip netns exec $NID ip link set eth1 up
# 配置网卡IP
$ sudo ip netns exec $NID ip addr add 192.168.1.1/24 dev eth1
# 设置默认网关
$ sudo ip netns exec $NID ip route add default via 192.168.1.254
```

通过这些操作，这样就手动为容器创建了网络设备，并配置好网络

> 如果需要访问容器外部的网络，就需要添加如下的IP NAT 地址伪装规则
>
> $ sudo iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -j MASQUERADE

