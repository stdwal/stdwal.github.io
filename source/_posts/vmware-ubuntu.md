---
title: Vmware Fusion下Ubuntu学习
date: 2018.5.19
---

[下载Ubuntu发行版](https://cn.ubuntu.com)

<!--more-->

#### 安装vmware tools
在虚拟机设置中点击安装vmware tools，获取一个VMwareTools-x.x.x-xxxx.tar.gz文件并解压。
打开终端并输入：
```bash
sudo ./vmware-install.pl -d
```
其中-d表示接受默认设置

#### 使用mac终端ssh连接到Ubuntu
在Ubuntu终端输入
```bash
sudo apt-get install openssh-server
```
安装openssh-server

使用```ifconfig```命令查看Ubuntu的IP地址

如果提示```Command 'ifconfig' not found```,使用
```bash
sudo apt install net-tools
```
安装net-tools后再使用```ifconfig```获取IP

![ifconfig](/images/ifconfig.png)

打开mac终端，输入
```bash
ssh [username]@[ip]
```
连接到对应的主机

如果有类似如下的提示
>
The authenticity of host '172.16.204.132 (172.16.204.132)' can't be established.
ECDSA key fingerprint is SHA256:FdGYxwkyB8V7+0DFAKvwX/A1NkomzxV/cJ/Ynp/iACo.
Are you sure you want to continue connecting (yes/no)? yes
>

输入yes建立连接

成功后的界面如下：

![ssh](/images/ssh.png)
