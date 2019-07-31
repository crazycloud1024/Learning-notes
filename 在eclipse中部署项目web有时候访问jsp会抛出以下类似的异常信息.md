---
title: 在eclipse中部署项目web有时候访问jsp会抛出以下类似的异常信息
date: 2018-07-25 20:58:17
categories: 杂项教程
tags: [Java,eclipse]
---
# 在eclipse中部署项目web有时候访问jsp会抛出以下类似的异常信息 # 

    org.apache.jasper.JasperException: The absolute uri: http://java.sun.com/jsp 

  部署web项目，如果抛出此异常。请保证 standard.jar与jstl.jar 两个jar包是否 在部署的项目中存在，如果不存在，请把，这两个包加入 /WEB-INF/lib 中。然后保证jsp中引用的版本地址 和 此版本地址是一致的：

    <%@ taglib uri="http://java.sun.com/jsp/jstl/core"prefix="c"%><%@ taglib uri="http://java.sun.com/jsp/jstl/fmt"prefix="f"%><%@ taglib prefix="fn"uri="http://java.sun.com/jsp/jstl/functions"%>



CSDN下载路径：[https://download.csdn.net/download/weixin_42216574/10416519](https://download.csdn.net/download/weixin_42216574/10416519)