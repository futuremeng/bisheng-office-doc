title: 毕升Office集成demo
author: 毕升Office
tags:
  - integrate
categories:
  - 集成
abbrlink: integrate-demo
date: 2020-01-01 22:22:00
keywords: 集成demo
description: 毕升Office集成demo
---
@[toc]

本文将介绍毕升office集成的demo。该demo可以从[demo](https://gitee.com/ibisheng/demo)得到。

## 配置运行demo

使用git 将demo下载到本地。

```shell
git clone https://gitee.com/ibisheng/demo
```

修改项目根目录下config.json

其中editorHost为毕升文档的地址，该值在demo中会用来拼接成使用毕升文档编辑文件或预览文件所需要的URL;

editorCaller为该demo运行所在的地址，该值在demo中会用来拼接为回调地址url，毕升文档从获取文件的URL，以及demo调用毕升文档是参数

apiKey为毕升文档api key，部署完毕升文档之后，可以从控制台许可信息中查询到该值。apiKey将被用来对调用参数进行Hmac md5签名。如果该值不对，调用文档时，将返回错误。

注意：配置文件url的值，需要使用demo服务器，毕升文档所在服务的IP,域名，主机名，不要使用localhost或者127.0.0.1。例如，我们在IP地址为192.168.2.40的服务上部署了毕升文档，在IP地址为192.168.2.66的服务器上运行demo，并且从毕升文档中获取了apikey，则有如下配置：

```
{
	"editorHost": "http://192.168.2.40",
	"editorCaller": "http://192.168.2.66:9090",
    "apiKey":"45ae1f8b5d50ea9322a3d8e3326ca0f9"
}
```

![image-20190325220838352](https://bisheng-public.oss-cn-zhangjiakou.aliyuncs.com/resource/image-20190325220838352.png)

### 在毕升Office中配置demo的回存地址

为了使得调用毕升文档对文件进行编辑之后，编辑结果能够回调到demo服务器，需要在毕升文档的配置文件中增加配置项，以指明如果是API调用编辑，编辑完成之后应该调用哪个地址来对结果进行回存。

毕升文档的配置文件在你安装的毕升文档目录的 workspace/config/config.yml

![image-20190325220009021](https://bisheng-public.oss-cn-zhangjiakou.aliyuncs.com/resource/image-20190325220009021.png)

编辑config.yml，在配置文件中增加API回调

```
apis:
   postapiurl: http://192.168.2.66:9090/api/file/saveBack
```

![image-20190325223245235](https://bisheng-public.oss-cn-zhangjiakou.aliyuncs.com/resource/image-20190325223245235.png)

修改毕升文档的配置之后，需要重启毕升文档服务

```
sh restart.sh
```

### 添加供测试的ms office文件

在项目根目录/storage下拷贝您要编辑的文件

### 运行项目

```
npm install

npm run build

npm run server
```

然后浏览器打开

[http://editorCaller](http://editorcaller/)

假如你是在本地运行demo的，可以直接 [http://localhost:9090](http://localhost:9090/)

## demo服务器端代码简介

在[demo](https://gitee.com/ibisheng/demo)中，当用户点击文件列表中的编辑按钮时，会进入到demo服务端代码，由相应的uri进行响应，该响应最后会生成一个调用毕升Office编辑的链接让浏览器打开；其中callURL参数在这个过程会生成。

![image-20190326173525577](https://bisheng-public.oss-cn-zhangjiakou.aliyuncs.com/resource/image-20190326173525577.png)

毕升Office服务器端在收到请求后，进过一系列的处理，最后会根据callURL参数的地址，请求demo服务器，获取本次编辑的数据。demo服务器响应，并按照格式返回数据

![image-20190326173823838](https://bisheng-public.oss-cn-zhangjiakou.aliyuncs.com/resource/image-20190326173823838.png)

同时demo服务器还会处理获取文件请求，把该文件返回给毕升Office服务器。当然在实际过程，这个地址可以各种可以下载文件的地址。例如：ftp,s3,普通http请求等。

![image-20190326174103980](https://bisheng-public.oss-cn-zhangjiakou.aliyuncs.com/resource/image-20190326174103980.png)



文件完成编辑之后，会回调到demo服务器，demo服务器响应了该请求，做出响应的处理。在实际过程中，集成方系统也需要对回存的文件进行处理

![image-20190326174526465](https://bisheng-public.oss-cn-zhangjiakou.aliyuncs.com/resource/image-20190326174526465.png)

