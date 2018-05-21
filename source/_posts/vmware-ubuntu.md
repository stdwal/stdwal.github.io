---
title: Vmware Fusion下Ubuntu学习
date: 2018.5.19
---

[下载Ubuntu发行版](https://cn.ubuntu.com)

<!--more-->

#### 安装vmware tools

在虚拟机设置中点击安装vmware tools，获取一个VMwareTools-x.x.x-xxxx.tar.gz文件并解压。

打开终端并输入以下命令以安装vmware tools

```bash
sudo ./vmware-install.pl -d
```

其中-d表示接受默认设置


#### 使用mac终端ssh连接到Ubuntu


安装openssh-server

```bash
sudo apt-get install openssh-server
```

使用 **ifconfig** 命令查看Ubuntu的IP地址

如果提示 *Command 'ifconfig' not found* ,安装net-tools后再使用 **ifconfig** 获取IP

```bash
sudo apt install net-tools
```

![ifconfig](/images/ifconfig.png)

打开mac终端，连接到对应的主机

```bash
ssh [username]@[ip]
```


如果有类似如下的提示
>The authenticity of host '172.16.204.132 (172.16.204.132)' can't be established.
>ECDSA key fingerprint is SHA256:FdGYxwkyB8V7+0DFAKvwX/A1NkomzxV/cJ/Ynp/iACo.
>Are you sure you want to continue connecting (yes/no)? yes


输入yes建立连接

![ssh](/images/ssh.png)
