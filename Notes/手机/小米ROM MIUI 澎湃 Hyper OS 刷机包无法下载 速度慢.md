---
title: 未命名
date: 2024-04-16 17:53:12
categories:
  - 分类
tags:
  - 标记
cover: https://img.itwoow.com/python.jpg
feature: false
published: false
comments: true
image-auto-upload: false
---
最近需要将红米K40手机降级到Android 11，于是找了对应的ROM下载，要么速度很感人，要么直接给你来个403 Forbidden。于是在网上找了一些相关资料。找了好几个方法，绕了几圈，总结了下面几个比较有效的方法
## 下载ROM地址
1. [MIUI官方ROM仓库](https://roms.miuier.com)
2. [小米ROM](https://xiaomirom.com/)
3. [MIUI历史版本](https://miuiver.com)

## 方法
### （最佳）修改域名
将bigota.d.miui.com替换成下面的其中一个域名。我觉得这个也是最佳的方法。
原地址：`https://bigota.d.miui.com/V12.5.19.0.RKHCNXM/alioth_images_V12.5.19.0.RKHCNXM_20211217.0000.00_11.0_cn_618e7057a1.tgz`
替换下面的其中一个域名，如：
`https://bkt-sgp-miui-ota-update-alisgp.oss-ap-southeast-1.aliyuncs.com/V12.5.19.0.RKHCNXM/alioth_images_V12.5.19.0.RKHCNXM_20211217.0000.00_11.0_cn_618e7057a1.tgz`

1. https://bkt-sgp-miui-ota-update-alisgp.oss-ap-southeast-1.aliyuncs.com/
2. https://cdnorg.d.miui.com/
3. https://bn.d.miui.com/

### MIUI Update info
此方法只能下载卡刷包。通过手机安装Updater([下载地址](https://github.com/YuKongA/Updater)),右上角可以登录小米账号，使用V2接口可获取当前账号拥有权限对应机型的 正式版内测/开发版公测 的详情信息。
这里我是下载红米的[K40的ROM](https://xiaomirom.com/rom/redmi-k40-poco-f3-alioth-china-fastboot-recovery-rom/)，从这个页面获取到Android 11的版本是V12.5.19.0.RKHCNXM。

`系统版本的填写有格式要求，MIUI14的格式是V14.0.4.0.AUTO，hyperOS正式版的格式是OS1.0.0.3.AUTO，hyperOS开发版的格式是OS1.0.24.1.8.DEV`

复制到App的系统版本中。
这样就可以获取到该版本的卡刷包

![](../../Resource/Assets/小米ROM%20MIUI%20澎湃%20Hyper%20OS%20刷机包无法下载%20速度慢/image-20240416190639271.jpeg)
### 修改Host
使用SwitchHost工具来修改Host，已经尝试了网上提供的大部分Host，均失败了。如图：

![](../../Resource/Assets/小米ROM%20MIUI%20澎湃%20Hyper%20OS%20刷机包无法下载%20速度慢/image-20240416184606900.png)
``` hosts
47.89.66.194 bigota.d.miui.com
47.89.66.194 hugeota.d.miui.com

#47.74.196.250 bigota.d.miui.com
#47.74.196.250 hugeota.d.miui.com

#183.136.207.132 bigota.d.miui.com
#183.136.207.132 hugeota.d.miui.com

#123.6.13.6 bigota.d.miui.com
#123.6.13.6 hugeota.d.miui.com

#110.43.121.13 bigota.d.miui.com
#110.43.121.13 hugeota.d.miui.com
```
## 下载
使用多线程下载工具，将下载速度最大化。如：IDM，迅雷等