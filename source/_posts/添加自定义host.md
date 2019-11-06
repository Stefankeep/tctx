---
title: 添加自定义host
date: 2019-11-06 13:12:55
tags: [notar,DNS,系统配置]
---
​	添加自定义的host，可以让本地电脑通过域名访问我们指定的服务器地址。在一些时候是非常有用的，比如自定义到github访问最好的服务器上面，可以有效的提高访问效率。再比如，我们开发的项目需要与其他团队进行协作，又需要通过新增本地域名来保护我们的数据库地址等不便公开的信息。

# Linux or Mac

​	`Linux`的所有发行版本，其中的配置文件都是在`/etc`下面，`Mac`也是同样需要注意的是修改`hosts`文件需要`root`权限来进行。

```shell
cat /etc/hosts
```

​	可以先看下`hosts`文件的内容

![deepin中hosts文件](http://q0j84xr3u.bkt.clouddn.com/201911061323.png)

​	简单来说，就是把要指定的`ip`写在一行左边然后，在右边写上自定义的域名名称，`hosts文件的优先级会默认高于dns解析结果或dns缓存`。

​	修改的方式就是

```shell
sudo echo "IP地址 域名" >> /etc/hosts
```

​	就能够添加自定义域名了。

​	在项目开发中，建议将一些敏感地址都使用这样的方式进行访问。

# Windows

​	`Windows`的`hosts`文件位置是`c:\windows\system32\drivers\etc`，修改方式跟Linux和Mac一样都是在文件中加入一行自定义的域名描述就行了。
