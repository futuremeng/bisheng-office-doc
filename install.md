title: 毕升Office 安装部署介绍
author: 毕升Office
tags:
  - install
categories:
  - 安装
abbrlink: install
date: 2020-01-01 23:36:00
keywords: 毕升Office本地部署安装
description: 毕升Office本地部署安装
---

@[toc]

在开始安装使用前请阅读毕升Office用户协议：[协议链接](https://bishengoffice.com/apps/blog/posts/agreement.html)

## 支持系统以及运行方式

毕升Office支持linux系统下使用docker的方式运行支持版本包括：centos7，ubuntu 18以及ubuntu系统衍生的国产系统。用户可以使用官方提供安装脚本实现一键安装单机版的毕升Office系统。

### docker 方式运行毕升Office

使用docker安装毕升Office首先需要按照官方文档准备好相应的安装脚本，然后按照文档中的步骤完成安装过程。使用过程中可以按照文档进行服务的升级，重启等操作，具体安装步骤参见链接：[使用docker中安装毕升Office](install-docker.html)

### 离线安装毕升Office

如用用户的服务器处于无法访问到外网，也可以按照相关步骤按照毕升Office，具体参考链接：[离线安装毕升Office](install-offline.html)

### 多结点集群部署以及流水线部署

同时毕升Office也支持集群安装多结点运行或者用户自己构建镜像实现流水线安装。具体可以联系毕升Office官方

## 系统更新

如果你的服务器无法从外网获取最新的镜像，参考[内网升级](install-offline.html#离线升级毕升office)；

如果你的服务器可以从外网下载最新的镜像，参考[脚本升级](install-problem.html#升级系统)

## 系统激活

以上各种安装运行方式对应的激活方式是一致。激活的具体参考文档参考链接：[激活毕升Office](install-active.html)

## 安装配置

在安装毕升Office时，你可以进行修改相关配置项：例如修改毕升Office默认的端口，添加字体等。具体参考链接：[修改安装配置](install-config.html)

## 第三方服务配置

毕升Office所依赖的第三方服务有：dgraph,mongodb,redis,minio等。如果用户需要修改毕升Office默认的第三方服务的配置或者使用自有的第三方服务，可以参考链接：[第三方服务配置](install-service.html)

## 安装问题排除

针对安装使用过程中的相关问题,可以参考问题排除链接进行故障排除：[问题排除](install-problem.html)

