---
title: docker中Mysql的备份以及恢复
date: 2019-11-17 11:46:23
tags: [docker,MySql]
---

使用`docker ps`查看mysql的容器名称

**比如MySQL的容器名称为mysql**

备份数据的脚本则为

```shell
#!/usr/bin/env bash

docker exec mysql mysqldump doc > doc.sql #使用docker exec让容器和本地交互,然后mysqldump来备份doc数据库,到处doc.sql文件作为备份文件
sed -i '1iSET FOREIGN_KEY_CHECKS = 0;' doc.sql #为导出的doc文件添加外键检测忽略,保障导入顺利
sed -i '1iSET NAMES utf8mb4;' doc.sql
sed -i '1iUSE doc;' doc.sql
echo "SET FOREIGN_KEY_CHECKS = 1;" >> doc.sql

echo "数据库备份完成"
```

恢复也比较简单(在my.cnf配置了client之后)

`docker exec -i mysql mysql doc < doc.sql` ,就可以将导出的`doc.sql`备份恢复到`doc`数据库里面

其中第一个`mysql`是容器名称,第二个`mysql`是执行的命令