---
title: SSH免密码登录
categories:
  - linux
tags:
  - 登录
cover: 'https://img.itwoow.com/python.jpg'
feature: false
date: 2023-12-16 10:29:21
---
## 生成证书

```shell
ssh-keygen
cd ~/.ssh
ls
```

私钥：id_rsa，公钥：id_rsa.pub。私钥需要保留好，这个相当于密码，有了这个文件，就可以访问我们的服务器。

打开公钥文件id_rsa.pub。可以看到公钥的内容如下，在阿里云，甲骨文，腾讯云可以复用这个公钥。

``` tex
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCott/pmUjcM67Hqh0KH8DLI/W0TpiMC2oluLvYUYHzEWUlhC2DB7r2+97zCNu85UxtuZhsSZktDk6HmFLNjCTt3L5j+wLeXab4D0641l9S1vN2UM1ov2fMckxDjG3V2SXGgqttvhKjRr+k7l5m5tYUHivlU3b7mbn9WBbc0X5Zqrpxq5VkOT4z3pqJCSMfwc3PpFmCZnuPE3ZfkWjObXDiNCKrRRYSRjZ0l0O5vZtUjrjj+GLXe24dSrNxjSdplI9XXPgF4wOcCiInrEuPBFQKO5gGTQ0Bi6vIIyrylyzgNGq3YeXaH6Jhu4UEIBKwzOi80LStVeaVuUjcDiSwkhtbIG3j/LWgAF9pB5vSBgffJm2em07KAklxaNiouYRSoq7N9fpwSntYFrgN87WUJqlptj8E+d5GmWTjI75ZkPhTlWgXLmsivGbLRQDGsASHLr+tttJcsxE7dWRThTjUVx/V9Zs9qmjidxWiYYulEFd1c9DYqzCasFiQ7CLgiYpR8FM=
```

## 设置服务器

1. 上传id_rsa.pub到服务器。假如上传的路径为~/is_rsa.pub

2. 设置授权key

   ``` shell
   : > /root/.ssh/authorized_keys#清空authorized_keys内容
   cat ~/is_rsa.pub >> /root/.ssh/authorized_keys#将公钥复制到authorized_keys
   ```

3. 重启SSH服务

   ```shell
   sudo service sshd restart
   ```

## 客户端连接

这里使用FinallShell，先新增连接，`认证方法`选择`公钥`。用户名为使用公钥登录的用户名。
<img src="https://images.itwoow.cn/images/2022/02/14/image-20220213175431218.png" alt="image-20220213175431218" style="zoom: 33%;" />

点击`私钥-浏览`，在弹出界面点`导入`，选择`id_rsa`文件，名称任意。
<img src="https://images.itwoow.cn/images/2022/02/14/image-20220213175941022.png" alt="image-20220213175941022" style="zoom: 33%;" />



## 其他（根据需要执行）

### Root用户登录

在腾讯云和甲骨文，使用SSH免密码登录，默认登录的用户是unbuntu：

``` shell
sudo -i
sudo sed -i 's/^#\?PermitRootLogin.*/PermitRootLogin yes/g' /etc/ssh/sshd_config;
```

### 启用密码登录

想要同时密码登录则执行下面这个命令：

```shell
sudo sed -i 's/^#\?PasswordAuthentication.*/PasswordAuthentication yes/g' /etc/ssh/sshd_config;
```

### 甲骨文，腾讯云一键SSH免密码Root登录

如果在甲骨文和腾讯云，在创建VPS的时候就绑定好公钥。但Ubuntu操作系统，密钥将默认绑定至ubuntu用户，而非root用户。在创建好VPS后，执行下面的脚本，就可以启用Root免密码登录：

``` shell
sudo -i
sudo sed -i 's/^#\?PermitRootLogin.*/PermitRootLogin yes/g' /etc/ssh/sshd_config;
#sudo sed -i 's/^#\?PasswordAuthentication.*/PasswordAuthentication yes/g' /etc/ssh/sshd_config;
: > /root/.ssh/authorized_keys
cat /home/ubuntu/.ssh/authorized_keys >> /root/.ssh/authorized_keys
# 删除UBUNTU用户
# deluser ubuntu --remove-home
sudo service sshd restart
```

