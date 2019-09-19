---
title: 'adb wifi 调试'
date: 2019-09-18 16:48:35
tags: []
published: true
hideInList: false
feature: 
---
## 连接手机和电脑

    命令行 adb devices 查看连接

![连接](https://user-gold-cdn.xitu.io/2018/7/5/1646a02e0977420b?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)
   
## 设置5555端口

    设置wifi调试端口 5555
    adb tcpip 5555

  ![端口](https://user-gold-cdn.xitu.io/2018/7/5/1646a02e09802302?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)
  
## 查看手机ip

    adb shell  ifconfig wlan0

 ![ip](https://user-gold-cdn.xitu.io/2018/7/5/1646a02e09607999?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)
 
    手机操作，打开 设置-WLAN-配置 页面，可看到当前 IP

![ip](https://user-gold-cdn.xitu.io/2018/7/5/1646a02e0bac8d18?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

## 连接手机 

    adb connect <手机ip地址>
    
![连接](https://user-gold-cdn.xitu.io/2018/7/5/1646a02e0bb32167?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)


## 成功
    连接成功显示

![成功](https://user-gold-cdn.xitu.io/2018/7/5/1646a02e5a3e35c7?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)
    
 锤子手机测试失败