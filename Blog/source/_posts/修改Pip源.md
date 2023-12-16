---
title: 修改Pip源
date: 2023-12-15 17:18:47
categories:
  - python
tags:
  - python
  - pip
cover: https://img.itwoow.com/python.jpg
feature: false
---
在使用pip安装依赖包时，由于网络原因，需要配置国内的源，解决速度慢问题。

---

**设置源**
====================

临时
-----------------

```cmd
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple 包名
```

默认
-------------------

> 使用命令行的方式：

``` cmd
pip config set global.index-url http://mirrors.cloud.tencent.com/pypi/simple
pip config set global.trusted-host mirrors.cloud.tencent.com
```

> 使用配置文件的方式

Window:
- 打开%USERPROFILE%\pip\ini（C:\Users\用户名\pip\pip.ini）如果没有pip目录则新建，建立pip.ini文件
- 文件内容如下：
```vim
[global]
index-url=http://mirrors.aliyun.com/pypi/simple/
[install]
trusted-host=mirrors.aliyun.com
```

Linux:
- 建立`~/.pip/pip.conf`文件
```cmd
vim ~/.pip/pip.conf
```
文件内容
```vim
[global] 
index-url = http://pypi.tuna.tsinghua.edu.cn/simple
trusted-host = pypi.tuna.tsinghua.edu.cn
```

`trusted-host`避免提示源不信任

**源列表**
====================

- 清华源：https://pypi.tuna.tsinghua.edu.cn/simple
- 腾讯源：http://mirrors.cloud.tencent.com/pypi/simple
- 阿里源：http://mirrors.aliyun.com/pypi/simple
- 豆瓣源：https://pypi.douban.com/simple/