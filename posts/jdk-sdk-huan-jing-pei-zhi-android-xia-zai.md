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
```
链接：https://pan.baidu.com/s/1AVshFeP9ZkT05xk_FIINag 
提取码：t8ci 
```


[Android studio sdk 下载地址](http://www.android-studio.org/index.php)

## jdk12

安装JDK11或以上版本，安装过程中没有提示安装JRE，进入JDK安装路径下 shift+右键 在此处打开命令窗口

  输入  
  ```
   bin\jlink.exe --module-path jmods --add-modules java.desktop --output jre   
  ```
  执行完成就会生成JRE
  
![](https://ouluqiang.github.io//post-images/1569573855494.png)


## jdk8
正常安装

## 环境变量配置

我的电脑右键，点击属性，点击高级系统设置，点击环境变量

![](https://ouluqiang.github.io//post-images/1569573876482.png)


新建系统变量，变量名写 JAVA_HOME 变量值 填写你JDK安装的路径

![](https://ouluqiang.github.io//post-images/1569573893452.png)


双击系统变量path编辑变量值 %JAVA_HOME%\bin;%JAVA_HOME%\jre\bin

![](https://ouluqiang.github.io//post-images/1569573915023.png)


在系统变量中新建 classpath 变量名，变量值为  %JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar

![](https://ouluqiang.github.io//post-images/1569573938408.png)


win+R运行输入cmd，输入java -version查看JDK版本和JVM信息

![](https://ouluqiang.github.io//post-images/1569573946973.png)



# sdk

新建系统变量ANDROID_HOME，变量值D:\xx\sdk（以你安装目录为准,确认里面有tools和add-ons等多个文件夹）

![](https://ouluqiang.github.io//post-images/1569573974734.png)


在系统变量PATH后面加上变量值;%ANDROID_HOME%\platform-tools;

![](https://ouluqiang.github.io//post-images/1569573987528.png)


win+R运行输入cmd，输入adb 查看

![](https://ouluqiang.github.io//post-images/1569574016688.png)
