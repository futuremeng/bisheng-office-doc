title: 毕升Office离线部署安装
author: 毕升Office
tags:
  - install
categories:
  - 安装
abbrlink: install-offline
date: 2020-01-01 23:30:00
keywords: 离线部署， docker离线安装
description: 毕升Office使用docker离线部署安装
---
@[toc]

离线安装毕升Office

毕升Office目前的安装方式使用docker安装。在安装过程中需要从阿里云镜像仓库中下载毕升Office所需要的镜像文件。但是在一些用户的环境中，由于服务器处于内网中，无法和外部网络联通,因此在安装过程中无法从外网下载镜像。

进行离线安装的前提是需要找一台和目标服务器的系统相同的可联网服务器，在可联网的服务器上下安装下文的步骤下载相关数据之后上传到目标服务器上

## 离线安装docker

这一步介绍如何安装毕升Office以来的docker服务。如果服务器上已经安装了docker服务器，则可以跳过这一步骤。

在离线的前提下，docker安装不能安装安装毕升Office提供的安装脚本进行了。因为毕升提供的安装脚本安装docker的前提条件是需要联网。内网环境下安装docker 资料可以参考链接：[使用二进制包安装docker](https://docs.docker.com/install/linux/docker-ce/binaries/) ;具体步骤可以概括为：

### 下载docker二进制包

进入docker[下载链接](https://download.docker.com/)选择您的硬件平台，然后下载.tgz与要安装的Docker CE版本相关的文件。建议选择稳定版本（stable版）。例如如果您的硬件上x86_64，操作系统为 linux 系统则选择链接 https://download.docker.com/linux/static/stable/x86_64/ 下载相应的版本。建议下载docker18版本。

### 解压二进制包

将下载的docker二进制包<span style="color:red">**上传到目标服务器上**</span>，然后进入到压缩包所在目录 运行命令  对软件包进行解压，并将解压后目录内的二进制文件拷贝到/user/bin中

```shell
tar xzvf  **.tar.gz
sudo cp docker/* /usr/bin/ 
```

### 启动docker 守护进程

```shell
sudo dockerd &
```

测试docker安装信息

```shell
docker -v 
```

## 安装docker-compose

1. 在可以联网的服务器上安装docker-compose

```shell
curl -L https://get.daocloud.io/docker/compose/releases/download/1.21.2/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
```

2. 将 /usr/local/bin/docker-compose 文件上传到目标服务器的 /usr/local/bin目录中。

3. 在目标服务器上给 docker-compose 文件相应的执行权限

   ```shell
   chmod +x /usr/local/bin/docker-compose
   ```

   

## 打包毕升Office的镜像文件

首先你需要找一台可以联网的服务器，然后下载毕升Office的安装脚本，然后将毕升Office的镜像下载到可以联网的服务器上。

```shell
git clone https://gitee.com/ibisheng/deploy.git  //下载安装脚本
cd deploy 
bash pullImage.sh latest // 拉去毕升Office所有的镜像
```

在执行完以上步骤后，可联网的服务器上就有了毕升Office全部镜像。

![image-20200222101628920](https://bisheng-public.nodoc.cn/resource/image-20200222101628920.png)

### 打包镜像以及离线加载镜像

将上图中的镜像打包。打包的基本命令是 docker save 镜像名称:tag  > 压缩包名称。例如以镜像 registry.cn-zhangjiakou.aliyuncs.com/bisheng/tools 为例，可以按照如下命令打包

```shell
docker save registry.cn-zhangjiakou.aliyuncs.com/bisheng/tools:latest > tools.tar
```

![image-20190628161854842](https://bisheng-public.nodoc.cn/resource/image-20190628161854842.png)

将上面打完包生成的文件上传到目标服务器上，并执行命令加载这个压缩包。

```shell
docker load < tools.tar  //在目录服务器上执行命令离线加载镜像
```

![image-20190628162228070](https://bisheng-public.nodoc.cn/resource/image-20190628162228070.png)

完成上步骤完成了一个镜像向内网服务器的离线加载工作。按照以上步骤对其他的几个镜像进行操作，即可将将毕升Office需要的镜像都导入到目标服务器上。

## 离线安装毕升Office

### 安装

在完成以上步骤之后，就可以按照毕升Office。在安装之前，需要将毕升office的安装脚本打包，然后上传到目标服务器上。上传完成之后进行解压，进入到安装脚本目录，执行 bash install.sh命令即可开始安装。

由于一些环境下脚本创建网络会失败，建议在一键安装前建议手动创建docker 网络 bisheng。

```shell
docker network create bisheng
```

在完成以上步骤之后，可以通过install.sh脚本来安装毕升Office

```shell
bash install.sh /bisheng_data   # 请确保/bisheng_data目录没有其他数据
```

注意：** 安装目录的结尾**不要 斜杠 “/”**，否则安装目录最好拼接会出错。**即上面脚本 "/bisheng_data"不要写成“/bisheng_data/”** 

**另外需要强调的是，不要使用有数据的目录作为安装目录，<span style="color: red;">*<u>因为初次安装过程中会清空该目录</u>*</span>**

该安装命令需要一个参数来指定安装目录，该目录是毕升Office的工作目录，所以的数据都会保存在该目录，需要保证该目录所有在的存储设备上有较大的空间。例如在上面的脚本是我们是使用 /bisheng_data目录作为安装目录

### 测试

待上一步骤脚本执行完成之后，先检查所有的docker容易是否全部正常启动。

```shell
docker ps -a
```

![image-20190402175256521](https://bisheng-public.nodoc.cn/resource/image-20190402175256521.png)

**其中tools这个容器正常状态是Exit的。**

### 如何使用

以上安装完成之后，输入地址 http://192.168.2.108 或者http://hostname  即可进入到毕升Office主页面。其中IP，hostname时安装毕升Office的服务器的IP或者主机名，或者指向该服务器的域名

## 离线升级毕升Office

如果你的系统在内网中使用，在需要升级毕升Office，由于不能直接从外网下载镜像，所以需要采用理想升级的方式。离线升级需要按照上文中的步骤先下载最新镜像，然后通过其他方式上传到内网，再加载镜像文件，最后执行bash upNode.sh命令即可。

### 下载镜像

在升级时，需要更新的镜像有： editor_app, editor, convert, drive_full四个镜像，再按照有docker的服务器上执行如下命令：

```bash
docker pull registry.cn-zhangjiakou.aliyuncs.com/bisheng/editor_app:latest
docker pull registry.cn-zhangjiakou.aliyuncs.com/bisheng/editor:latest
docker pull registry.cn-zhangjiakou.aliyuncs.com/bisheng/drive_full:latest 
docker pull registry.cn-zhangjiakou.aliyuncs.com/bisheng/convert:latest 
```

### 导出打包镜像

在下载完镜像后，需要将下载的镜像导出，打包为.tar文件。依次执行如下命令：

```
docker save registry.cn-zhangjiakou.aliyuncs.com/bisheng/editor_app:latest > editor_app.tar
docker save registry.cn-zhangjiakou.aliyuncs.com/bisheng/editor:latest > editor.tar
docker save registry.cn-zhangjiakou.aliyuncs.com/bisheng/drive_full:latest > drive_full.tar
docker save registry.cn-zhangjiakou.aliyuncs.com/bisheng/convert:latest > convert.tar
```

### 将镜像文件上传到内网服务器

在导出打包完镜像之后，采用某种可行的方式将 editor_app.tar editor.tar drive_full.tar convert.tar四个文件上传到内容服务器上。

### 导入镜像文件

在内网目标服务器上导入上一步中上传的四个镜像文件

```bash
docker load < editor_app.tar
docker load < editor.tar
docker load < drive_full.tar 
docker load < convert.tar
```

### 升级

在你内网服务器上执行命令进行升级即可：

```bash
bash upNodes.sh
```

