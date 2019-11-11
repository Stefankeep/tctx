---
title: 清除CentOS的多余elrepo以及优化yum下载
date: 2019-11-09 17:03:01
tags: [CentOS,yum]
---

# 优化下载

`yum-axelget` 是 EPEL 提供的一个 yum 插件。使用该插件后用 yum 安装软件时可以并行下载，大大提高了软件的下载速度，减少了下载的等待时间，安装该插件的同时会安装另一个软件 axel。axel 是一个并行下载工具，在下载 http、ftp 等简单协议的文件时非常好用： 

```shell
sudo yum install yum-axelget
```

# 删除多余elrepo

```shell
ls /etc/yum.repos.d/ #列出当前系统源
sudo rm 上边列出的不需要的源文件(eg:epel.repo) #多个使用空格分隔
ls /etc/pki/rpm-gpg/ #列出多个源的KEY
sudo rm 上边删除的源将KEY也删除掉(eg:RPM-GPG-KEY-EPEL) #比如说要删除的是eple.repo源，则他的KEY名称为RPM-GPG-KEY-EPEL
rpm -qa | grep 系统源的名称(eg:epel) #列出源的rpm文件
sudo yum remove 列出的rmp文件(eg:epel-release-6-8.noarch) #删除源的rpm文件
sudo yum clean all #清除yum缓存
sudo yum repolist #列出现在的yum源
sudo yum makecahe #创建新的索引和yum缓存
```

