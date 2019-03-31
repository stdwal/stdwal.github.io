---
title: CentOS下安装MariaDB
date: 2019.3.31
---

## 使用yum安装MariaDB
```
yum -y install mariadb
```
```
yum -y install mariadb-server
```

## 启动MariaDB
```
systemctl start mariadb
```

## 对MariaDB进行配置
```
mysql_secure_installation
```

## 修改MariaDB的字符集
```
vi /etc/my.cnf.d/client.cnf
```
在[client]下添加
```
default-character-set=utf8
```

```
vi /etc/my.cnf.d/mysql-client.cnf
```
在[mysql]下添加
```
default-character-set=utf8
```

```
vi /etc/my.cnf.d/server.cnf
```
在[mysqld]下添加
```
character-set-server=utf8
```

## 重启MariaDB
```
systemctl restart mariadb
```

## 查看修改情况
进入MariaDB
```
mysql -u root -p
```

查看字符集
```
SHOW VARIABLES LIKE 'character%';
```
