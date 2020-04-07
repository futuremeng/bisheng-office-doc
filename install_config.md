title: 毕升Office安装配置
author: 毕升Office
tags:
  - install
categories:
  - 安装
abbrlink: install-config
date: 2020-01-01 23:32:00
keywords: 毕升office nginx配置，水印配置，添加字体
description: 毕升Office安装配置
---

@[toc]

本文将介绍毕升Office相关配置。主要内容包括：修改毕升office nginx 服务端口，如何关闭预览和编辑模式下的水印，添加用户自定义字体。

## 修改毕升Office nginx服务端口

毕升Office安装完成后，默认使用的端口为80，因此如果你需要使用其他的端口，可以安装如下步骤来进行。

修改方法是进入到安装目录/bisheng_data（**如果你安装目录不是bisheng_data，请将bisheng_data替换为你的安装目录）**；然后进入到 nginx目录中，修改docker-compose.yml。例如：毕升Office部署脚本在  /bisheng_data目录中，则进入到 /bisheng_data/nginx

![image-20190326002904765](https://bisheng-public.nodoc.cn/resource/image-20190326002904765.png)

修改 docker-compose.yml文件。

![image-20190327123431581](https://bisheng-public.nodoc.cn/resource/image-20190327123431581.png)

完以上步骤之后，重新安装毕升Office。

重新安装的方法为进入到**毕升Office部署脚本所在的目录**（**即你从github或者gitee上clone的的目录的位置，不是安装目录**） ；执行

```shell
 sh reinstall.sh
```

等待重启完毕升Office，接口使用新的IP:新端口（域名或者主机名）访问毕升Office。



## 毕升Office水印

目前所有的用户可以去水印，新装用户在激活时可以自动获得去水印的权限。用户可以在控制台看到自己去水印的权限：

![image-20190422161751270](https://bisheng-public.nodoc.cn/resource/image-20190422161751270.png)

接下来可以在控制台设置是否关闭水印以及自定义水印。

![image-20190422162208506](https://bisheng-public.nodoc.cn/resource/image-20190422162208506.png)

完成以上设置之后，用户需要重启毕升Office

重启毕升Office的方法是，执行脚本 restart.sh

```shell
bash restart.sh
```

## 字体设置

毕升Office默认情况下是使用客户端字体。此时在部署完成之后，不需要做字体上的处理。同时也可以配置所客户端都是用从服务器上下载字体。具体配置是修改 /bisheng_data/workspace/config/config.yml，在改配置文件中添加如下配置：

```shell
fonts:
  localfonts: 2 #此时将禁用客户端本地字体。
```

在完成以上配置之后，你可以在服务上添加相关字体

毕升Office的运行需要提供一定的字体文件。在公开发行的安装脚本中含有常用的免费字体。同时毕升Office提供工具让用户自己添加字体。

在添加字体前，同时在你使用该工具时你必须明白如下条款：

1. <span style="color:red;font-weight:bold">你对使用的字体有使用版权</span>，
2. <span style="color:red;font-weight:bold">毕升office并不提供这些字体的使用版权，因为字体而导致的一切法律责任和毕升office无关</span>

### 如何使用毕升Office脚本工具添加字体

在完成毕升Office安装之后，你可以使用毕升Office安装脚本提供的字体服务脚本来添加你自己的字体。该脚本是你**下载的毕升Office安装脚本目录中的**fontService.sh。在运行该脚本前，**需要把你的字体拷贝到一个空目前下**，然后使用该目录作为参数运行fontService.sh 脚本。例如，我在运行该脚本前在/root目录下创建了一个空目录 fonts，并且拷贝了我的字体到这个目录下，那么我可以使用如下命令 

```shell
bash fontsService.sh /root/fonts
```

![image-20190505111231311](https://bisheng-public.nodoc.cn/resource/image-20190505111231311.png)

### 如何删除你添加的字体

如果你需要删除你添加的字体，可以安装如下步骤进行：

1. 进入到你下载的毕升Office安装脚本目录下，如果你以前添加过字体，你会看到一个userFonts的目录

   ![image-20190505111752953](https://bisheng-public.nodoc.cn/resource/image-20190505111752953.png)

2. 如果你是需要删除全部的字体，你可以把这个目录删除，如果你需要删除某一个字体，你需要进入到userFonts目录中，删除相应的字体。

3. 完成第二步中的删除之后，运行fontService.sh 脚本。这次运行不需要带参数。

   ```shell
   bash fontsService.sh //不需要带参数了！
   ```

当fontService.sh脚本执行之后，你删除的字体就冲毕升Office系统中移除了。

## 修改发行版本

毕升Office部署默认会使用free这个tag版本，但是有时候需要切换到其他发行版。例如在API开发过程中，为了调试方便，可以切换到test这个tag版本。

首先，进入到部署脚本所在的目录，在部署脚本所在的目录下会有一个.config文件，该文件记录着上次安装时安装的目录以及IP还有安装的发行版本(该问默认是不可见的，执行执行vi .config进行修改)。

![image-20190326110041787](https://bisheng-public.nodoc.cn/resource/image-20190326110041787.png)

打开后，可以看到该文件中，只有一行，三个参数使用空格分割。其中**第一个为安装目录，第二个IP，第三个为使用的发行版本。**

![image-20190326110618356](https://bisheng-public.nodoc.cn/resource/image-20190326110618356.png)

在已经部署的情况下修改部署版本需要将editor_app, editor,convert,tools,drive_full(如果有)容器删除。然后运行upgrade.sh脚本

```shell
sh upgrade.sh
```

安装会下载新的发行版本，等待安装完成即可。
