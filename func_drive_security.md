title: 毕升Office团队协同
author: 毕升Office
tags:
  - security
categories:
  - 云盘配置
abbrlink: drive-security
date: 2020-01-01 22:08:00
keywords: 创建团队，团队协同,云盘配置
description: 毕升Office团队协同，云盘配置登陆
---
@[toc]
# 云盘配置

本文介绍云盘相关的配置。

### 禁止创建账号

默认情况下，毕升Office在部署完成之后，在主页点击登陆之后在登陆界面中有注册账号的链接，并且可以允许用户自由注册账号。如下图



![image-20200211173042092](https://bisheng-public.nodoc.cn/resource/image-20200211174606428.png)

如果你根据自己的策略，需要关闭帐户注册，可以在系统配置文件中进行配置。配置文件的位置在： /bisheng_office/workspace/config/config.yml

编辑配置文件，增加如下配置项

```yaml
security:
  disablesignup: true
```

完成以上配置文件的修改之后，重启服务即可生效。重启方法是进入到毕升Office的安装脚本目录，运行脚本 upNodes.sh即可

```bash
bash upNodes.sh
```

在完成以上配置以后，在登陆页面上注册入口将关闭。

![image-20200211174606428](https://bisheng-public.nodoc.cn/resource/image-20200211174606428.png)
