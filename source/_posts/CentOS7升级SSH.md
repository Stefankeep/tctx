---
title: CentOS7 升级SSH
date: 2019-11-07 12:37:46
tags: [ssh,CentOS,Linux维护]
---

# 升级前准备

### 关闭`SELinux`

```shell
sudo setenforce 0 #临时关闭SELinux
```

### 安装/升级，SSH相关的依赖

```shell
sudo yum install -y gcc openssl-devel pam-devel rpm-build
```

### 下载SSH原文件

到[OpenSSH](http://ftp.openbsd.org/pub/OpenBSD/OpenSSH/portable/)下载最新版本，后缀名为`tar.gz`的源文件。

国内访问下载缓慢可以使用

[子原的分享-openssh-8.1p1.tar.gz](http://res.zyarcher.com/openssh-8.1p1.tar.gz)来下载`8.1`版本的`OpenSSH`。

下载方式可以使用`scp`或者`ftp`上传到服务器，也可以使用`wget`直接进行下载。

使用`wget`方式：

```shell
sudo yum install -y wget #安装wget，若已存在则会提示已安装
sudo wget '下载链接，如 http://ftp.openbsd.org/pub/OpenBSD/OpenSSH/portable/openssh-8.1p1.tar.gz' #使用wget进行下载
```

然后使用 `tar xf 文件名`进行解压缩。

### 卸载原SSH

```shell
sudo rpm -qa |grep  openssh #查看当前ssh
sudo for i in $(rpm -qa |grep openssh);do rpm -e $i --nodeps;done #删除现有ssh
sudo rm -rf /etc/ssh #移除当前ssh配置
```

# 安装/升级 SSH

使用`cd`进入解压后的目录。

```shell
sudo ./configure --prefix=/usr --sysconfdir=/etc/ssh --with-md5-passwords--with-pam --with-tcp-wrappers  --with-ssl-dir=/usr/local/ssl --without-hardening #脚本配置命令
sudo make && make install #编译安装
sudo cp contrib/redhat/sshd.init /etc/init.d/sshd #拷贝系统初始sshd
sudo chkconfig --add sshd #新增sshd服务
sudo chkconfig sshd on #开启sshd服务
sudo chkconfig --list|grep sshd #查看sshd服务是否已经开启
sudo sed -i "32a PermitRootLogin yes" /etc/ssh/sshd_config #允许root账号登录
sudo systemctl restart sshd #重启sshd服务
systemctl status sshd #查看是否启动成功
ssh -V #查看当前ssh版本
```

`sshd服务`即我们通常使用的`SSH`。**完成以上步骤，即已完成ssh的升级。**