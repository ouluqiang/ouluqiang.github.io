---
title: 'jdk sdk环境配置 Android下载'
date: 2019-09-24 17:16:07
tags: []
published: true
hideInList: false
feature: 
---
# 下载

[jdk下载地址](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

jdk下载  
链接：https://pan.baidu.com/s/1AVshFeP9ZkT05xk_FIINag 
提取码：t8ci 



[Android studio sdk 下载地址](http://www.android-studio.org/index.php)

## jdk12

安装JDK11或以上版本，安装过程中没有提示安装JRE，进入JDK安装路径下 shift+右键 在此处打开命令窗口

  输入  
  ```
   bin\jlink.exe --module-path jmods --add-modules java.desktop --output jre   
  ```
  执行完成就会生成JRE
  
![image.png](https://upload-images.jianshu.io/upload_images/2025676-092ee2d81fb4e15d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## jdk8
正常安装

## 环境变量配置

我的电脑右键，点击属性，点击高级系统设置，点击环境变量

![image.png](https://upload-images.jianshu.io/upload_images/2025676-80d9883df2172e7d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

新建系统变量，变量名写 JAVA_HOME 变量值 填写你JDK安装的路径

![image.png](https://upload-images.jianshu.io/upload_images/2025676-14e4cad6e48b11de.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

双击系统变量path编辑变量值 %JAVA_HOME%\bin;%JAVA_HOME%\jre\bin

![image.png](https://upload-images.jianshu.io/upload_images/2025676-b05e6e5352c1876b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在系统变量中新建 classpath 变量名，变量值为  %JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar

![image.png](https://upload-images.jianshu.io/upload_images/2025676-84140d49e227ae06.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

win+R运行输入cmd，输入java -version查看JDK版本和JVM信息

![image.png](https://upload-images.jianshu.io/upload_images/2025676-b5bff947bcc4d526.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


# sdk

新建系统变量ANDROID_HOME，变量值D:\xx\sdk（以你安装目录为准,确认里面有tools和add-ons等多个文件夹）

![image.png](https://upload-images.jianshu.io/upload_images/2025676-7c4474ac5ea494cc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在系统变量PATH后面加上变量值;%ANDROID_HOME%\platform-tools;

![image.png](https://upload-images.jianshu.io/upload_images/2025676-95aa820c636c3cd8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

win+R运行输入cmd，输入adb 查看

![image.png](https://upload-images.jianshu.io/upload_images/2025676-a43222dcc6f1aa70.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)