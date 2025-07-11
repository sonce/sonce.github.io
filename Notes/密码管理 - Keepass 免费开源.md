---
title: 未命名
date: 2024-04-22 15:39:52
categories:
  - 分类
tags:
  - 标记
cover: https://img.itwoow.com/python.jpg
feature: false
published: true
comments: true
image-auto-upload: false
---
## 基础设置
### 插件管理

### 中文包
### mstsc远程
`cmd://cmd /c "cmdkey /generic:TERMSRV/{S:SERVER} /user:{S:DOMAIN}{USERNAME} /pass:"{PASSWORD}" && start /b mstsc /v:{URL:RMVSCM} && timeout /t 10 /nobreak && cmdkey /delete:TERMSRV/{URL:RMVSCM}"`

`cmd://cmd /c "cmdkey /generic:TERMSRV/{URL:HOST} /user:{USERNAME} /pass:{PASSWORD} && mstsc /v:{BASE:RMVSCM} && cmdkey /delete:TERMSRV/{URL:HOST}"`
https://github.com/kvaps/keepass-url-overrides
https://keepass.info/help/base/placeholders.html
### 自动输入
`%O{IME:OFF}{CLEARFIELD}{DELAY 1000}{URL:RMVSCM}{Tab}{DELAY 500}{CLEARFIELD}{USERNAME}{ENTER}{DELAY 2000}{CLEARFIELD}{PASSWORD}+{DELAY 100}{ENTER}`

#### QQ,TIM无法输入
`RDCAutoTypeAndTCATO`
![](../Resource/Assets/密码管理%20-%20Keepass%20免费开源/image-20240423160033660.png)


### 同步
1. WebDav
2. 网盘
### 锁定配置
`C:\Users\用户名\AppData\Roaming\KeePass\KeePass.config.xml`
重命名为`KeePass.config.enforced.xml`并剪切到Keepass的程序根目录
`"D:\Program Files\KeePass Password Safe 2\KeePass.config.enforced.xml"`
## Chrome导入
![](../Resource/Assets/密码管理%20-%20Keepass%20免费开源/image-20240422154618183.png)
## 设置
### 软件设置
### 高级
1. 
![](../Resource/Assets/密码管理%20-%20Keepass%20免费开源/image-20240423083217607.png)


### 记录设置
1. 双通道自动输入混淆
   ![](../Resource/Assets/密码管理%20-%20Keepass%20免费开源/image-20240423082838402.png)
   2. 多应用共用账号
   ![](../Resource/Assets/密码管理%20-%20Keepass%20免费开源/image-20240423083959732.png)
   ![](../Resource/Assets/密码管理%20-%20Keepass%20免费开源/image-20240423083842388.png)


## 插件
1. KPSourceForgeUpdateChecker
2. KPEnhancedEntryView
3. KPEntryTemplates
4. KeeTrayTOTP
   [Github](https://github.com/settings/security)--Authenticator app--Setup Key
   多因素验证MFA,2FA神器
   ![](../Resource/Assets/密码管理%20-%20Keepass%20免费开源/image-20240506084619394.png)


1. YetAnotherFaviconDownloader
   下载网站的图标
6. WebAutoType
   快速添加记录
6. AutoTypeSearch
   自动输入热键没有匹配到记录时，它会弹出一个搜索框，搜索到所需记录后点击即可使用自动输入。还可以为AutoTypeSearch设置一个全局搜索热键：在keepass主界面中点击【工具】→【选项】→【AutoTypeSearch】→勾选【Show when system-wide hot key is pressed:】→点击下方输入框→按下你想使用的全局热键。
8. [keepassnatmsg](https://github.com/smorks/keepassnatmsg)+[keepassxc-browser](https://github.com/keepassxreboot/keepassxc-browser)
   浏览器插件
## 参考资料
https://post.smzdm.com/p/713042/