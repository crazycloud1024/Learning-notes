---
title: umask总结
date: 2017-03-29 09:14:00
categories: 杂项
---
# umask总结
### umask：反向掩码
- umask值 可以用来保留在创建文件权限
- 新建FILE权限: 666-umask
    - 如果所得结果某位存在执行（奇数）权限，则将其权限+1
- 新建DIR权限: 777-umask	
- 非特权用户umask是 002
- root的umask 是 022
- umask: 查看
- umask #: 设定
    - umask 002
- umask –S 模式方式显示
- umask –p 输出可被调用
- 全局设置： /etc/bashrc 用户设置：~/.bashrc