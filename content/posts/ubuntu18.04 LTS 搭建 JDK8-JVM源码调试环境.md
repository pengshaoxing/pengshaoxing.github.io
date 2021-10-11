---
title: "Ubuntu18.04 LTS 搭建 JDK8-JVM源码调试环境"
date: 2021-10-11T23:30:30+08:00
draft: false
---

# ubuntu18.04 LTS 搭建 JDK8-JVM源码调试环境

## 适用环境
本教程适用以下环境：
- ubuntu 18.04 LTS

## 环境准备

### jdk环境
预先安装好需要对应版本的JDK.我调试的是jdk1.8 那就先装好jdk1.8

jdk下载地址:[https://adoptopenjdk.net/]

### 编译环境
```bash
apt install make cmake gcc g++ zip unzip build-essential gdb
#安装 mercurial
sudo apt install mercurial
```


## 源码下载

```bash
mkdir open-jdk8 && cd open-jdk8
hg clone https://hg.openjdk.java.net/jdk8u/jdk8u openjdk
chmod u+x get_source.sh 
./get_source.sh
```

## 编译设置

```bash
chmod u+x configure
./configure --with-debug-level=slowdebug --with-native-debug-symbols=internal
```
`--with-debug-level=slowdebug`参数是为了显示更多调试信息.hotspot编译之后是一个名为libjvm.so的动态库,在java命令里通过dlopen加载,如果不加上`--with-native-debug-symbols=internal`,需要手动解压`jdk/lib/amd64/server`里的libjvm.debuginfo,或者指定动态库路径,调试起来会很复杂
将`hotspot/make/linux/makefiles/gcc.make`中的`WARNINGS_ARE_ERRORS = -Werror`改为W`ARNINGS_ARE_ERRORS = -w,`不然出现warning就会出现如下异常.

### 缺失x11库

配置错误信息：
```bash
configure: error: Could not find X11 libraries. You might be able to fix this by running 'sudo apt-get install libX11-dev libxext-dev libxrender-dev libxtst-dev libxt-dev'.
configure exiting with result code 1

```
解决方案：
按照提示安装软件，其中`libX11-dev`可能是没有的。可以去掉
```bash
sudo apt-get install libX11-dev libxext-dev libxrender-dev libxtst-dev libxt-dev
```

### 缺少cups库文件

```bash
checking cups/cups.h usability... no
checking cups/cups.h presence... no
checking for cups/cups.h... no
checking cups/ppd.h usability... no
checking cups/ppd.h presence... no
checking for cups/ppd.h... no
checking for cups headers... no
configure: error: Could not find cups! You might be able to fix this by running 'sudo apt-get install libcups2-dev'. 
configure exiting with result code 1
```
解决方案：按照提示安装软件

```bash
sudo apt-get install libcups2-dev
```
### 缺少freeType

```bash
checking for FREETYPE... no
configure: error: Could not find freetype! You might be able to fix this by running 'sudo apt-get install libfreetype6-dev'. 
configure exiting with result code 1
```

解决方案：按照提示安装软件

```bash
sudo apt-get install libfreetype6-dev
```


### 缺少ALSA

```bash
checking for ALSA... no
checking alsa/asoundlib.h usability... no
checking alsa/asoundlib.h presence... no
checking for alsa/asoundlib.h... no
configure: error: Could not find alsa! You might be able to fix this by running 'sudo apt-get install libasound2-dev'. 
configure exiting with result code 1

```

解决方案：

```bash
sudo apt-get install libasound2-dev
```

### 缺少fontconfig

```bash
checking fontconfig/fontconfig.h usability... no
checking fontconfig/fontconfig.h presence... no
checking for fontconfig/fontconfig.h... no
configure: error: Could not find fontconfig! You might be able to fix this by running 'sudo apt-get install libfontconfig1-dev'. 
configure exiting with result code 1
```
解决方案：

```bash
sudo apt-get install libfontconfig1-dev
```
### 配置检查成功

```bash
====================================================
A new configuration has been successfully created in
/home/peng/openjdk8/build/linux-x86_64-normal-server-slowdebug
using configure arguments '--with-debug-level=slowdebug --with-native-debug-symbols=internal'.

Configuration summary:
* Debug level:    slowdebug
* JDK variant:    normal
* JVM variants:   server
* OpenJDK target: OS: linux, CPU architecture: x86, address length: 64

Tools summary:
* Boot JDK:       openjdk version "1.8.0_242" OpenJDK Runtime Environment (AdoptOpenJDK)(build 1.8.0_242-b08) OpenJDK 64-Bit Server VM (AdoptOpenJDK)(build 25.242-b08, mixed mode)  (at /home/peng/.sdkman/candidates/java/8.0.242.hs-adpt)
* Toolchain:      gcc (GNU Compiler Collection)
* C Compiler:     Version 7.5.0 (at /usr/bin/gcc)
* C++ Compiler:   Version 7.5.0 (at /usr/bin/g++)

Build performance summary:
* Cores to use:   3
* Memory limit:   3934 MB
```

## 编译

```bash
make all
```

漫长的等待。。

## vscode 导入hotspot源码远程调试



## clion 导入hotspot源码远程调试



参考连接：

https://rqsir.github.io/2019/04/19/openjdk-8-%E4%BD%BF%E7%94%A8Clion%E8%B0%83%E8%AF%95%E6%BA%90%E7%A0%81/

https://zhuanlan.zhihu.com/p/63752092

https://zhuanlan.zhihu.com/p/50220757