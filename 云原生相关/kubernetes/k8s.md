kubernetes笔记:
- 存活探针检查
k8s在做存活探针检查的时候一般要设置一个初始化延迟来说明应用程序的启动时间，以免pod重复重启。  initialDelaySeconds : 第一次探测等待的时间。
- 删除一个ReplicationController
  当使用kubectl delete删除ReplicationController时，pod也会被删除，但是由于ReolicationController创建的的pod并不是ReplicationController的组成部分，只是由它管理，所以就可以只删除ReplicationController并保持pod运行，命令如`kubectl delete rc $name --cascade=false`
- 配置服务上的会话亲和性：
  当多次执行同样的命令，想让特定的客户端产生的所有请求每次都指向同一个pod，可以设置服务的sessionAffinity属性为ClientIP（而不是None,None是默认值）。