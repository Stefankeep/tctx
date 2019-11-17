---
title: docker以及docker-compose的安装
date: 2019-11-17 11:17:15
tags: [docker,docker-compose,运维]
---

# docker安装

`docker`的安装最好是按照官网的推荐来进行。

[docker官网]( https://docs.docker.com/ )

简要记录下再`CentOS`下如何执行安装。

* 清除现有docker软件
* 添加docker的yum源，添加docker源的密钥
* 进行安装
* 清理docker的yum源([参考文章]( [https://zyarcher.com/2019/11/09/%E6%B8%85%E9%99%A4CentOS%E7%9A%84%E5%A4%9A%E4%BD%99elrepo/](https://zyarcher.com/2019/11/09/清除CentOS的多余elrepo/) ))
* 使用国内阿里云镜像加速器

### 清除现有docker软件

```shell
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

### 安装所需依赖，添加源以及密钥

```shell
sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

### 进行安装

```shell
sudo yum install docker-ce docker-ce-cli containerd.io
sudo systemctl start docker #启动docker服务
sudo docker run hello-world #测试是否可用
sudo systemctl enable docker #开机自动启动
```

在这一步，一般服务器都没有问题，个人主机的话容易遇到安装之后`docker使用权限的问题`。**如果安装和使用的都是root账号,不需要进行下面的操作**

解决方式官网也给出了指导。

[官网解决docker权限]( https://docs.docker.com/install/linux/linux-postinstall/ )

```shell
sudo groupadd docker #添加一个用户群组
sudo usermod -aG docker $USER #将当前用户加入到docker群组里面
# 然后需要重新注销再登录一下用户,如果还是不能进行使用,请运行下面两行代码进行赋权
sudo chown "$USER":"$USER" /home/"$USER"/.docker -R 
sudo chmod g+rwx "$HOME/.docker" -R 
```

# docker-compose安装

`docker-compose`官网是直接给出的可执行文件,所以安装很简单,唯一的麻烦可能就只是国内下载基本上太难了.

网络比较好的直接进入[官网]( https://docs.docker.com/compose/install/#install-compose ),按照指导进行下载使用.

执行完之后不能直接进行使用的,是因为没有软连接或者加入可执行权限(软连接即快捷方式)

```shell
sudo chmod +x /usr/local/bin/docker-compose #添加docker-compose可执行权限
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose #添加docker-compose软连接
```



还可以通过国内我自己的资源进行下载

[docker-compose-1.24.1-Liunx-x86_64](http://res.zyarcher.com/docker-compose-1.24.1-Liunx-x86_64)

**需要注意**

通过我自己的资源进行下载的需要进行重命名

```shell
wget http://res.zyarcher.com/docker-compose-1.24.1-Liunx-x86_64 #下载docker-compsoe1.24.1把呢不能
mv docker-compose-1.24.1-Liunx-x86_64 /usr/local/bin/docker-compose #将docker-compose重命名以及移动到一般可执行文件的目录下
sudo chmod +x /usr/local/bin/docker-compose #赋可执行权
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose #添加软连接
```

以上就已经安装完成

执行`docker-compose --version`进行验证

# docker国内加速器

目前推荐使用aliyun进行,注册账号之后,按照阿里云的配置进行操作,有详细说明很简单.

在完成注册后打开[容器镜像服务]( https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors )就好了.

