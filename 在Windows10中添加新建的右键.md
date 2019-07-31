---
title: 在Windows10中给右键中添加新建的方式
date: 2018-04-26 20:15:17
categories: 杂项教程
tags: windows
---
## 使用管理员权限打开window命令提示符输入如下即可 ##


    cmd /k reg add "HKEY_CLASSES_ROOT\Directory\Background\shellex\ContextMenuHandlers\New" /ve /t REG_SZ /d {D969A300-E7FF-11d0-A93B-00A0C90F2719} /f
