title: 毕升Office 激活
author: 毕升Office
tags:
  - install
categories:
  - 安装
abbrlink: install-active
date: 2020-01-01 23:35:00
keywords: 毕升Office
description: 毕升Office部署安装
---
@[toc]

没有完成系统激活前对毕升Office的访问都会被重定向到控制台以激活系统。激活系统主要是完成对毕升Office的免费**授权**。该授权包括系统中可注册的用户数，同时编辑最大的文档数，版本有效性验证，该账号付费升级详情等信息。每一个激活的毕升Office都会有一个唯一的许可序列号。

毕升Office目前支持**联网激活**和**手动离线激活**两种验证方式。

使用系统内置管理员账号登录，登录成功后，会自动转到系统控制台，即可以看到激活入口。**内置管理员用户名是:admin**；**内置默认密码是:bisheng**

![image-20190321114019579](https://bisheng-public.oss-cn-zhangjiakou.aliyuncs.com/resource/image-20190321114019579.png)

## 联网激活

如图上图所示，点击进入许可配置，即可开始联网激活，按照页面中提示，就可以激活系统

![image-20190321114414158](https://bisheng-public.oss-cn-zhangjiakou.aliyuncs.com/resource/image-20190321114414158.png)



## 手动离线激活

1. 点击离线激活之后，进入到离线激活的页面，*点击蓝色字"点击激活"* 

![image-20190321114746455](https://bisheng-public.oss-cn-zhangjiakou.aliyuncs.com/resource/image-20190321114746455.png)

2. 当点击这个链接之后，**会打开一个毕升的文档的激活页面**，在页面中填写相关信息之后，点击激活。在激活成功之后会得到一个毕升系统生成的激活码。

   ![image-20190321115340995](https://bisheng-public.oss-cn-zhangjiakou.aliyuncs.com/resource/image-20190321115340995.png)

3. 激活成功之后，在页面的下方会生成激活码，拷贝这些激活码

   ![image-20190321115535841](https://bisheng-public.oss-cn-zhangjiakou.aliyuncs.com/resource/image-20190321115535841.png)

4. 回到你部署的毕升Office控制台激活页面，点击离线激活页面中的激活按钮

   ![image-20190321120301071](https://bisheng-public.oss-cn-zhangjiakou.aliyuncs.com/resource/image-20190321120301071.png)



点击激活之后，在输入框填入上一步拷贝字符串，然后点击确定，完成计划

![image-20190321120432815](https://bisheng-public.oss-cn-zhangjiakou.aliyuncs.com/resource/image-20190321120432815.png)



完成激活系统之后可以在许可信息页面看到

![image-20190321120518232](https://bisheng-public.oss-cn-zhangjiakou.aliyuncs.com/resource/image-20190321120518232.png)

## 删除激活

如果你想删除许可重新激活，点击删除许可就可以。删除许可会将许可信息彻底清空**。请谨慎使用**

## 激活生效方式

激活完成之后需要重启[毕升Office](https://bishengoffice.com/)，运行restart.sh即可

```shell
sh restart.sh
```

![image-20190228102954857](https://bisheng-public.oss-cn-zhangjiakou.aliyuncs.com/resource/image-20190228102954857.png)

完成以上步骤之后，即可开始使用毕升Office

接下来你可以通过地址： http://IP正常使用

### 许可的再次验证

当用户完成激活之后，部署在用户服务器上的毕升Office将获取一个许可。为了保证一个许可只有一台服务器运行，服务器上获取的许可将在90天后重新进行重新验证。

1. 在用户部署的毕升Office服务器可以连接到毕升官方许可服务器的情况下，重新验证是自动进行的，不需要任何干预。
2. 如果用户部署的毕升Office服务器在内网环境中无法连接到毕升官网许可服务器，则需要管理员进行人工干预，过程和离线激活过程一致。

**当一个许可在多台服务器上<u>同时</u>运行时**，再下一次的重新验证时，只有第一台服务器的的验证能够通过，如果第二台服务进行验证将会失败，并且导致该许可被**封禁**。如果一个许可只有一台服务器在运行，则不会发生这种情况。

## 商业许可的获取

目前毕升Office的商业付费版和免费版的安装过程是一致的。两者的区别是在获取不同的许可之后商业版将会自动开通商业版的功能。

当用户需要商业付费版许可时：

1. 首先在服务器上安装毕升Office，然后按照激活的过程进行激活。此时毕升Office将以免费版进行运行
2. 联系毕升Office官方，并告诉需要升级为商业版的服务器许可序列号
3. 收到毕升官方升级商业许可完成的通知之后，使用离线激活重新激活该服务器或者在许可配置页面刷新许可
4. 使用脚本重启毕升Office服务

![image-20200223114350146](https://bisheng-public.nodoc.cn/resource/image-20200223114350146.png)



激活完成之后需要重启[毕升Office](https://bishengoffice.com/)，运行restart.sh即可

```shell
sh upNodes.sh
```

![image-20190228102954857](https://bisheng-public.oss-cn-zhangjiakou.aliyuncs.com/resource/image-20190228102954857.png)

