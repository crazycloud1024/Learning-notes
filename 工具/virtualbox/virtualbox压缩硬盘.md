### vitrualbox压缩硬盘

由于virturabox只会增大分配磁盘空间，不会自动释放空间，同时不提供对虚拟磁盘的释放操作界面，但virtualbox提供了命令行压缩磁盘的操作。
<hr>
##### 操作

指导思想:

1、虚拟机: 清理系统，卸载、删除系统垃圾文件

2、虚拟机: 将磁盘数据靠“前”移动，并将剩余磁盘空间写“零”

3、物理主机: 清除“零”字节空间，使用 VBoxManage modifyhd 工具压缩 VDI 磁盘镜像文件

Linux 虚拟机

1、虚拟机: 清理、卸载系统垃圾文件

2、填0操作（如果是多个盘，请分别执行）
```shell
sudo dd if=/dev/zero of=/EMPTY  bs=1M

sudo rm -f /EMPTY
```


3、物理主机: 执行 “VBoxManage modifyhd /the-path-of-VDI.vdi –compact”

例如在virtualbox安装目录打开命令行，执行：

```shell
$ ./VBoxManage.exe modifyhd D://virtualbox/centos7.5-docker/7.5-disk.vdi -compact
0%...10%...20%...30%...40%...50%...60%...70%...80%...90%...100%
```
中间的是磁盘路径。 

vm虚拟机格式
如果你的虚拟硬盘是Vmware的VMDK格式，那就要麻烦点，因为VirtualBox不支持直接压缩VMDK格式，但是可以变通下：先转换成VDI并压缩，再转回VMDK。执行命令：
```shell
VBoxManage clonehd "source.vmdk" "cloned.vdi" --format vdi
VBoxManage modifyhd cloned.vdi --compact
VBoxManage clonehd "cloned.vdi" "compressed.vmdk" --format vmdk
```
