---
title: 升级CentOS内核版本
date: 2019-11-09 17:03:23
tags: [CentOS,Linux]
---

# 查看当前的内核

```shell
uanme -r #查看当前内核版本
uname -a #查看完整内核信息
cat /etc/redhat-release #查看CentOS发行版版本
```

# 升级内核

`ELRepo` 仓库是基于社区的用于企业级 Linux 仓库，提供对 RedHat Enterprise (RHEL) 和 其他基于 RHEL的 Linux 发行版（CentOS、Scientific、Fedora 等）的支持。  ELRepo 聚焦于和硬件相关的软件包，包括文件系统驱动、显卡驱动、网络驱动、声卡驱动和摄像头驱动等。同时，`ELRepo`是升级内核的必要Yum仓库，提供最新的各种软件包，但非官方源，其中部分内容未经官方进行测试升级，对于一些软件有新版本需求时，仍不失为一种最佳解决方案。

```shell
sudo rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org #导入ELRepo仓库的公共密钥
sudo rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-3.el7.elrepo.noarch.rpm #安装ELRepo仓库的yum源，会自动安装最新的符合版本的仓库
sudo yum clean all #清楚当前yum的各种缓存和索引
sudo yum makecache #建立新的yum的缓存和索引
sudo yum update -y #更新系统中的软件包
sudo yum --disablerepo="*" --enablerepo="elrepo-kernel" list available #列出最新的内核版本 --disablerepo指不使用哪些仓库 
sudo yum --enablerepo=elrepo-kernel install kernel-ml #安装最新的版本 --enablerepo指使用那个yum仓库
sudo awk -F\' '$1=="menuentry " {print i++ " : " $2}' /etc/grub2.cfg #查看当前安装之后系统中的内核
sudo grub2-set-default 0 #使用最新安装的内核作为系统开机默认的grub，即开机使用哪个内核进行系统启动
sudo grub2-mkconfig -o /boot/grub2/grub.cfg #修改完成之后需要重新生成grub的配置文件
reboot #重启以启用新的内核
```

# 删除旧的内核

删除旧的内核推荐使用`yum-utils`工具进行，默认一般是安装的，可以安装一下以确认。`yum-utils`工具会保留最新的三个内核版本，超过三个会自动删除旧版本的内核。

```shell
sudo yum install -y yum-utils #安装yum-utils
sudo package-cleanup --oldkernels #清楚旧的内核
```

