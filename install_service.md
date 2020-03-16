title: 毕升Office依赖的第三方服务
author: 毕升Office
tags:
  - install
categories:
  - 安装
abbrlink: install-service
date: 2020-01-01 23:28:00
keywords: 毕升office mongodb配置, 毕升office redis配置, 毕升office rabbitmq配置,毕升office dgraph配置,毕升office minio配置,毕升office nginx配置
description: 毕升Office部署安装第三方服务配置
---
@[toc]

# 毕升Office依赖的第三方服务

毕升office的运行以来如下第三方服务：

## mongodb

用于存储编辑过程中的中间数据以及系统的相关配置。用户在部署过程中可以使用毕升Office提供的mongodb安装程序，也可以使用自己部署的mongodb。如果需要使用用户自己部署的mongodb，可以安装如下步骤进行：

### 修改mongodb连接地址

找到配置文件config.yml，修改mongodb配置，将数据库连接指向自己的部署的数据。注意填写的地址必须是局域网内能访问到的地址。 配置文件config.yml在如下位置： /bisheng_data/config/config.yml;  

![image-20200223111834221](https://bisheng-public.nodoc.cn/resource/image-20200223111834221.png)

### 关闭默认的mongodb

修改完以上配置之后，重启Office服务之后，毕升Office将使用用户部署的mongodb。但是此时毕升Office提供的默认mongodb还是在运行。用户可以关闭毕升Office提供的mongodb。在docker部署的条件下，修改docker-compose文件： /bisheng_data/service/docker-compose.yml，删掉如下图中部分：

![image-20200223111928825](https://bisheng-public.nodoc.cn/resource/image-20200223111928825.png)

完成以上修改之后，可以运行 docker rm mongod 删除默认mongoldb。然后执行 upNodes.sh重启结点

## redis

redis是作为系统的缓存使用的。用户也可以使用自己部署的redis，过程和使用用户部署的redis过程类似。在这里不重复这个过程。

## Rabbitmq

Rabbitmq 是用来同步各个结点状态。**只有docker安装时以来rabbitmq。**用户也可以使用自己部署的rabbitmq。过程如mongodb类似。

## dgraph

建议用户不要更换。更换过程和上面的服务类似。

## minio

文件存储服务。用于系统中文件存储层封装。在商业版中可以更换为oss, aws s3等商业对象存储。具体咨询毕升Office官方。

## nginx

Nginx 作为服务反向代理服务器，将各类请求转向到后台。在docker安装时，nginx被封装在docker中，如果用户需要使用自己nginx，可以将 nginx配置文件拷贝到自己的nginx目录下，并进行相关配置。建议一般用户不要更新nginx，毕升官方不负责指导更换nginx相关事宜

### 端口修改

修改方法是进入到安装目录/bisheng_data（**如果你安装目录不是bisheng_data，请将bisheng_data替换为你的安装目录）**；然后进入到 nginx目录中，修改docker-compose.yml。例如：毕升Office部署脚本在  /bisheng_data目录中，则进入到 /bisheng_data/nginx

![image-20190326002904765](https://bisheng-public.oss-cn-zhangjiakou.aliyuncs.com/resource/image-20190326002904765.png)

修改 docker-compose.yml文件。

![image-20190327123431581](https://bisheng-public.oss-cn-zhangjiakou.aliyuncs.com/resource/image-20190327123431581.png)

完以上步骤之后，重新安装毕升Office。

重新安装的方法为进入到**毕升Office部署脚本所在的目录**（**即你从github或者gitee上clone的的目录的位置，不是安装目录**） ；执行

```shell
 sh reinstall.sh
```

等待重启完毕升Office，接口使用新的IP:新端口（域名或者主机名）访问毕升Office。

