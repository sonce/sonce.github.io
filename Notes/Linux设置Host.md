---
title: Linux设置Host
date: 2024-01-03 18:05:06
categories:
  - linux
  - ubuntu
tags:
  - linux
  - ubuntu
cover: https://img.itwoow.com/python.jpg
feature: false
published: true
comments: true
image-auto-upload: false
---
### Ubuntu系统
在Ubuntu系统中，你可以通过以下步骤来设置HOST：

1. 打开终端。
2. 输入
``` shell
   sudo vim /etc/hosts
```
回车键。这将使用vim编辑器打开hosts文件。
3. 在打开的文件中，你可以添加或修改你想要的HOST条目。每一行应该以IP地址开始，后面跟着一个或多个主机名，每个主机名之间用空格分隔。
``` txt
   192.168.1.19   nfs.domain.net nfs
```
4. 完成编辑后，按 `Esc` 键，然后输入 `:wq` 并按回车键保存并退出vim编辑器。
5. 最后，重启网络以使更改生效，可以通过在终端中输入:
``` shell
   apt-get install network-manager
   service network-manager restart
```
请注意，这些操作需要管理员权限，因此在执行这些命令时可能需要输入你的密码。另外，任何对hosts文件的更改都应该谨慎进行，因为不正确的设置可能会导致网络问题。

### Windows
### 测试
安装Ping
```shell
sudo apt-get install iputils-ping
ping [domain]
```
### github
https://gitlab.com/ineo6/hosts/-/raw/master/hosts