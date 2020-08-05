#### 使用confd和registrator创建动态负载均衡器

需求： 当容器启动或停止时候，能动态更新配置

实现：

在应用程序运行容器之前，放置一个Nginx负载均衡器，用于将访问请求分发到应用程序的容器上。这些容器会自动注册到Consul键值存储上，这是由registrator来实现，而confd会从Consul拉取信息，然后写入到Nginx的配置文件，最后负载均衡器（Nginx）使用新的配置文件重启。

步骤：

```shell
# 启动 Consul 容器
$ docker run -d -p 8400:8400 -p 8500:8500 -p 8600:53/udp -h cookbook progrium/consul -server -bootstrap -ui-dir /ui

# 启动 registrator 容器
$ docker run -d -v /var/run/docker.sock:/tmp/docker.sock -h 192.168.137.221 gliderlabs/registrator -ip 192.168.137.221 consul://192.168.137.221:8500/elb

# 启动两个应用程序，这里使用nginx
$ docker run -d -p 81:5000 nginx:1.17-alpine
$ docker run -d -p 82:5000 nginx:1.17-alpine
```

```shell
# 创建负载均衡器
配置nginx反向代理:
➜  /tmp cat /etc/nginx/nginx.conf
events {
    worker_connections 1024;
}

http {
    upstream elb {
      server 192.168.137.221:81;
      server 192.168.137.221:82;
    }

    server {
        listen 80;
        location / {
            proxy_pass http://elb;
        }
    }
}

$ docker run -d -p 80:80 -v ./nginx.conf:/etc/nginx/nginx.conf --name elb nginx:1.17-alpine
```

下载并配置confd

```shell
# 下载confd二进制, 创建资源模板文件夹
sudo mkdir -p /etc/confd/{conf.d,templates}

# 在/etc/confd/conf.d下创建config.toml文件
[template]
src = "config.conf.tmpl"
dest = "/etc/nginx/nginx.conf"
keys = [
    "/elb/hostname",
]
reload_cmd = "docker restart elb"

# 然后编辑模板放在templates目录下
>> config.conf.tmpl
events {
    worker_connections 1024;
}

http {
    upstream elb {
      {{range getvs "/elb/hostname/*"}}
        server {{.}};
      {{end}} 
      
    }

    server {
        listen 88;
        location / {
            proxy_pass http://elb;
        }
    }
}

# confd 单次
$ confd -onetime -backend consul -node 192.168.137.221:8500

# confd 轮询
$ confd -backend consul -interval 5 -node 192.168.137.221:8500
```

最后，在守护进程模式下启动confd。设置一个很小的时间从Consul拉取信息的间隔，然后可以随意启动或者停止应用程序的容器。然后你会看到，每次在你启动或者停止一个应用程序容器时，confd都会动态更新配置文件并且重启elb容器。