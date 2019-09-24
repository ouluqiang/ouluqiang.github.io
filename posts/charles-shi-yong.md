---
title: 'charles使用'
date: 2019-09-23 18:38:43
tags: [工具,Charles]
published: true
hideInList: false
feature: 
---
charles基本设置使用

<!-- more -->


## 抓包工具charles学习版
```
链接：https://pan.baidu.com/s/1aDRIDVu9y66niOZOnPcMDQ 
提取码：sj13 
```

## http抓包
代理 > 代理设置中设置端口号 8888 ,勾选启用HTTP代理
![代理设置](https://ouluqiang.github.io//post-images/1569304039210.jpg)

![端口](https://ouluqiang.github.io//post-images/1569303949066.jpg)

帮助 > 本地ip 查看电脑ip
![帮助](https://ouluqiang.github.io//post-images/1569304145156.jpg)

![ip](https://ouluqiang.github.io//post-images/1569304186481.jpg)

手机和电脑在一个网段(连接同一个路由器)，在手机网络设置里面填写代理服务地址信息
电脑ip ,  端口号

![代理](https://ouluqiang.github.io//post-images/1569304740083.jpg)

在使用手机进行网络请求时，charles会有一个提示，允许即可

## https抓包
https需要安装证书，注意电脑安装证书需要安装到受信任的颁发机构

![证书](https://ouluqiang.github.io//post-images/1569305139356.jpg)

![安装证书](https://ouluqiang.github.io//post-images/1569305350226.jpg)

手机也需要安装证书，在浏览器下载证书,如果不识别，不能安装，建议用谷歌浏览器app下载安装
```
chls.pro/ssl
```
![手机证书](https://ouluqiang.github.io//post-images/1569305456616.jpg)

勾选Windows代理，打开ssl代理设置
![ssl设置](https://ouluqiang.github.io//post-images/1569305597175.jpg)

添加主机和端口号， 主机用*，端口号443抓取https，也可以用*
![](https://ouluqiang.github.io//post-images/1569305786428.jpg)

## Android 7.0抓https失败的解决方案

新建network_security_config.xml文件
![network_security_config](https://ouluqiang.github.io//post-images/1569302838764.png)

在network_security_config.xml文件写入以下代码

```
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <base-config cleartextTrafficPermitted="true">
        <trust-anchors>
            <certificates src="system" overridePins="true" />
            <certificates src="user" overridePins="true" />
        </trust-anchors>
    </base-config>
</network-security-config>
```

在AndroidManifest.xml添加配置  

![networkSecurityConfig](https://ouluqiang.github.io//post-images/1569302899586.png)


```
android:networkSecurityConfig="@xml/network_security_config"
```