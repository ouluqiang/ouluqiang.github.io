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

![连接](https://ouluqiang.github.io//post-images/1568884026016.png)

   
## 设置5555端口
设置wifi调试端口 5555
```
    adb tcpip 5555
```

![端口](https://ouluqiang.github.io//post-images/1568886196625.png)

  
## 查看手机ip
```
    adb shell  ifconfig wlan0
```

![ip](https://ouluqiang.github.io//post-images/1568886240609.png)

 
手机操作，打开 设置-WLAN-配置 页面，可看到当前 IP

![ip](https://ouluqiang.github.io//post-images/1568886275147.png)


## 连接手机 
```
    adb connect <手机ip地址>
```

![连接](https://ouluqiang.github.io//post-images/1568886318164.png)



## 成功
连接成功显示

![成功](https://ouluqiang.github.io//post-images/1568886341285.png)

    
 锤子手机测试失败