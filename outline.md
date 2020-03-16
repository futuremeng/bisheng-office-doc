title: 毕升Office文档导航
author: 毕升Office
tags:
  - integrate
  - install
  - API
categories:
  - 集成
  - 安装
abbrlink: guide
date: 2020-02-21 9:21:00
keywords: 文档导航
description: 毕升Office文档汇总，导航
---


## 安装

### 如何一键部署

如果你的服务器可以从外网获取内容，可以参考如何使用官方提供安装脚本在centos 或ubuntu系统中一键部署部署Office：参考[链接](install-docker.html)

### 如何在内网部署

如果你的服务器是网络隔离，无法获取外网中的内容：参考相关步骤按照毕升Office，参考[链接](install-offline.html)

### 如何系统激活

毕升Office系统如何进行激活以及激活的相关问题参考[链接](install-active.html)

### 如何升级系统

1. 如果你是内网部署参考[内网升级](install-offline.html#%E7%A6%BB%E7%BA%BF%E5%8D%87%E7%BA%A7%E6%AF%95%E5%8D%87office)；

2. 如果你是使用脚本部署参考[脚本升级](install-problem.html#%E5%8D%87%E7%BA%A7%E7%B3%BB%E7%BB%9F)

### 安装配置

1. 如何修改默认端口：参考[链接](install-config.html#修改毕升office-nginx服务端口)

2. 如何添加字体：参考[链接](install-config.html#添加用户自定义字体)

3. 如何设置水印：参考[链接](install-config.html#毕升office水印)

### 配置第三方服务

如何将毕升Office默认所依赖的mongodb,redis等第三方服务更换为用户自己的服务参考[链接](install-service.html)

### 相关问题排查

1. 安装完成后管理员账号不存在参考[链接](install-problem.html#管理员登陆账号不存在)

2. 如何修改用户名密码参考[链接](install-problem.html#修改管理员密码)

3. 许可异常无法更新处理参考[链接](install-problem.html#许可过期之后无法重新激活)

4. 重启服务器之后恢复服务参考[链接](install-problem.html#重启系统以及系统恢复)

5. 控制台中报错误：nutrition.js找不到参考[链接](install-problem.html#无法访问到nutritionjs)

## 集成

毕升Office可以与现有系统进行集成以赋予现有系统在线预览/编辑Office的能力。集成相关问题参考如下文档：

### 集成方式

1. 集成毕升Office需要的数据：参考[链接](integrate.html#集成毕升office所需要的数据)

2. 集成方式一：直接传递参数。参考[链接](integrate.html#集成毕升office方法一直接传递数据)

3. 集成方式二：使用callURL获取参数。参考[链接](integrate.html#集成毕升office方法二使用callurl来传递数据)

### 文件回存

在系统集成了毕升office之后，文件编辑完成之后生成了新的文件之后将通过文件回存的方式通知集成系统获取新文件。具体内容参考相关[链接](integrate-savback.html)

如果你想在回存时获取文件的修订记录，批注数据，以及需要得到文件对应的PDF文件，参考[链接](integrate-savback.html#回存数据类型配置)

### 集成相关配置

1. 如何开启集成的安全验证，对集成调用进行验证具体参考[链接](integrate-config.html#毕升office请求签名)
2. 集成时指定文档打开的权限，例如何禁止文件下载，打印，只允许文件以只读模式打开（或者文件预览时，不显示编辑的入口），请参考[链接](integrate-config.html#文件打开的权限)
3. 如何使用PDF方式预览文件参考[链接](integrate-config.html#使用pdf方式预览文件)
4. 为不同的文档指定不同的回存地址以实现和多个系统的集成参考[链接](integrate-config.html#为每个文档指定回存的地址)
5. 在回存时获取文档的修订，批注，以及对应的PDF文件：[参考链接](integrate-savback.html#回存数据类型配置)

### 集成demo

毕升Office官方提供demo介绍参考[链接](integrate-demo.html)

## API

### 功能API

功能API包括：

1. 删除文件
2. 移除文件中的协同者
3. 文件重命名
4. 主动回存
5. 文件预处理
6. 查询文档打开状态
7. 拷贝正在编辑的文件
8. 查询文件内容指纹，该功能主要用于验证文档是否被修改。
9. 查询文件的历史版本

以上API说明参考[链接](integrate-rest-api.html)

API安全验证参考[链接](integrate-rest-api.html#api安全验证)

### Office行为控制API

编辑器的行为控制主要有：

1. 控制编辑主菜单，以及默认打开的菜单，例如，文档打开时设置默认的开启的菜单是协同。
2. 控制某些功能的默认状态：例如强制文档在打开时开启修订模式，关闭添加批注功能，
3. 在菜单上去掉一些按钮：例如去掉关于按钮，去掉logo等。
4. 在预览模式下设置不可拷贝文档内容。
5. 设置文档在预览/编辑时打开时的默认缩放比例
6. 定义选中文字之后的自定义处理函数。

以上控制方式主要是通过加载配置文件或者调用Office时的参数进行控制。具体参考文档[链接](integrate-ui-api.html)

### 相关数据处理API

数据处理API包括在后端替换文档中内容，插入签章，公文套红等方案。具体请[咨询我们](aboutus.html)









