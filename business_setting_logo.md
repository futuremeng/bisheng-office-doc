title: 毕升Office自定义logo
author: 毕升Office
tags:
  - API
categories:
  - 功能接口
abbrlink: integrate-logo
date: 2020-01-01 22:09:00
keywords: 自定义logo
description: 毕升Office自定义logo
---
@[toc]
毕升Office提供自定义Office方式。在使用之前需要先确认你部署的毕升office是否具备相应的权限。或者联系官方咨询相关信息。

待管理员给你部署的毕升Office升级之后，你需要重新离线激活之后，然后重启毕升Office才能进行自定义logo的操作。其中离线激活参考链接：[手动离线激活](https://www.bishengoffice.com/apps/blog/posts/license.html#手动离线激活) ；重启毕升Office方式是进入到毕升Office安装脚本目录下，执行 restart.sh脚本即可。

```shell
bash restart.sh
```



## 准备素材，并上传

当完成以上步骤之后，你需要准备如下素材：1. logo.png 图片作为logo,大小为30x30像素；2. favicon.ico 图标，作为浏览器标签页上的logo，大小为16x16。

准备好以上素材之后，你需要做的只是把上面的素材上传到毕升Office所做的服务器上。具体的目录是 /bisheng_data/workspace/temp 目录。其中 /bisheng_data目录为你安装毕升Office目录，在实际过程中请按照你实际的安装目录进行操作。例如：我上传了自己的素材到了毕升Office所在的虚拟机上。

![image-20190515153224054](https://bisheng-public.nodoc.cn/resource/image-20190515153224054.png)

## 重启服务

完成以上步骤之后，你需要做的就是重启毕升Office服务。你可以使用重启脚本来重启，或者你只需要重启docker 结点 drive_full。

```shell
bash restart.sh
```

或者

```shell
docker restart drive_full
```

重启完成之后，进入到  /apps/console中检查你的许可是否已经升级成功：

![image-20190515154224042](https://bisheng-public.nodoc.cn/resource/image-20190515154224042.png)

## 检查效果

当完成以上步骤之后，你可以在自己部署的部署Office系统中检查自定义的logo是否工作正常

![image-20190515153649599](https://bisheng-public.nodoc.cn/resource/image-20190515153649599.png)

或者你可以在浏览器地址栏中打开链接：http://ip/static/office/custom/logo ，http://ip/resource/custom/favicon.ico 查看logo是否更换

![image-20190515153929407](https://bisheng-public.nodoc.cn/resource/image-20190515153929407.png)

## 自定义logo转跳链接

logo转跳链接是指点击logo图片时转跳的地址，默认会转跳到毕升Office首页。商业版用户可以自行定制该链接的地址。定制方法为：使用管理员登录进入到控制台（/apps/console），点击“设置”标签页即可设置。如下图

![image-20190621173613189](https://bisheng-public.nodoc.cn/resource/image-20190621173613189.png)

