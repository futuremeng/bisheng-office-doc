title: 毕升Office部署安装问题排查
author: 毕升Office
tags:

  - install
categories:
  - 安装
abbrlink: install-problem
date: 2020-01-01 23:29:00
keywords: 管理员无法登陆 修改管理员密码 无法激活 系统重启 系统升级
description: 毕升Office使用docker部署安装问题处理
---
@[toc]

## 管理员登陆账号不存在

激活时使用管理员登录显示admin用户不存在，出现这种情况，应该是数据库初始化在安装过程中出现错误。

![0CA357AABE39F8ABDC340C24EF3A8E87](https://bisheng-public.nodoc.cn/resource/0CA357AABE39F8ABDC340C24EF3A8E87.png)

### docker 安装处理办法

处理办法是：首先检查docker 容器 dgraphServer和dgraphZero是否存在以及正常运行。如果有问题，可以执行脚本 reinstall.sh来重新创建docker结点。

![image-20190412095104051](https://bisheng-public.nodoc.cn/resource/image-20190412095104051.png)

在确保docker正常之后，需要重新初始化数据，执行脚本 initTools.sh 加上参数 8即可：

```shell
bash initTools.sh 8
```


## 修改管理员密码

带有云盘的版本登陆之后，可以进入修改密码的地址/auth/changePassword 进行密码修改，也可以由管理员进入后台，对用户进行密码重置。

由用户自己修改密码参考下图：

![image-20200218113454784](https://bisheng-public.nodoc.cn/resource/image-20200218113454784.png)

管理员在后台修改密码参考下图

![image-20200218113840662](https://bisheng-public.nodoc.cn/resource/image-20200218113840662.png)



## 许可过期之后，无法重新激活
建议你先升级系统，如果升级完系统还无法解决问题，你可以按照步骤清除本地许可
如果你的许可过期之后，重新激活是一直失败，可以运行脚本cleanLicense.sh

```shell
bash cleanLicense.sh 
```

执行完成之后，本地的许可将被清空，可以重新按照激活流程激活。

## 重启系统以及系统恢复：

1. docker安装版本按照如下步骤重启系统：

```shell
bash upNodes.sh
```


## 升级系统

docker部署版本

执行脚本upgrade.sh

```shell
bash upgrade.sh
```

## 无法访问到nutrition.js

如果系统在安装完成之后，或者升级之后，无法打开文件，请查看浏览器控制台，是否有访问 nutrition.js的错误。如有有可以执行安装字体脚本

```bash
bash fontService.sh
```

