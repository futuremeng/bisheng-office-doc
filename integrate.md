title: 毕升Office集成
author: 毕升Office
tags:

  - integrate
categories:
  - 集成
abbrlink: integrate
date: 2020-01-01 22:24:00
keywords: 毕升Office集成
description: 毕升Office集成
---
@[toc]

毕升Office所有的版本都拥有集成的能力，可以与用户的系统继续集成。在进行Office集成时，用户需要完成两件事：1. 提供毕升office集成必要的用户数据。2. 实现文件文件回存的接口。

## 集成毕升Office所需要的数据

在集成使用Office预览/编辑文件时集成方需要向Office提供本次操作的文档基本以及当前用户的信息。具体的的数据格式如下：

```json
{
    doc: {
       docId: "", 
       title: "",
       fetchUrl: "",
    },
    user: {
       uid: "",
       nickName: "",
       avatar: ""
     },
}
```

返回数据中的<span style="color:red">**doc是本次预览/编辑的文档的信息，user 是本次打开文件进行预览/编辑的用户的信息**</span>。文档信息和用户信息详细含义如下：

### 文档信息各数据项含义

​	 docId: 文档的ID，不同的文档通过docId 来区分。

​         title: 文件的标题，用于编辑时在编辑器上显示当前文件的标题，<span style="color:red">**需包含加上文件扩展名**</span>

​         fetchUrl: 该文件的获取链接。当毕升Office会通过这个链接去获取文件。<span style="color:red">**该链接不要进行编码**</span>

### 用户信息各数据项含义

​        uid：用户ID，不同的用户通过uid来做区分

​        nickName： 用户昵称。用于多人编辑时标示不同的用户。

​        avatar：用户头像地址。用于多人编辑时 在右上角显示各个用户

### 对于用户信息的强调说明：

在集成过程中用户信息是必须传入的。<span style="color:red">并且在调用Office对文件进行编辑时，不同的用户的uid必须是不同的</span>。不同的用户使用同一个uid将导致重复uid的用户被强制下线。

### 其他数据项：

以上数据是在毕升office中打开一个文档的必须数据的最简集合。在后续相关文档中会逐步介绍其他数据项的意义以及使用场景。

## 集成毕升Office方法一：直接传递数据

该方法的基本原理是在**对数据进行简单**处理之后在毕升Office的**地址中**使用参数来传递数据。对于简单应用场景（例如：Office文件预览，简单编辑等需求）可以使用这种方法。这种方式的具体使用方法如下：

预览文件的URL：http://**bisheng_host**/apps/editor/openPreview?data=XXX

编辑文件的URL：http://**bisheng_host**/apps/editor/openEditor?data=XXX

其中data的值为上文中提到的所需的json数据<span style="color:red">**首先经过序列化**</span>得到字符串,然后<span style="color:red">**对字符串进行进行base64编码**</span>得到的值。data值首先会在毕升Office服务器上进行解析得到本次文件预览/编辑的数据，然后调用相应的Office服务进行处理。

例如：本次预览/编辑操作所需的文件以及用户信息如下：

```json
{
    doc: {
       docId: "bishengoffice", 
       title: "毕升Office测试.docx",
       fetchUrl: "https://bisheng-upload.nodoc.cn/system/毕升Office免费私有化部署手册.docx"
    },
    user: {
       uid: "admin",
       nickName: "管理员",
       avatar: "https://bisheng-upload.nodoc.cn/system/defaultAvatar.png"
     },
}
```

首先对以上json数据进行序列化：得到如下字符串

{"doc":{"docId":"bishengoffice","title":"毕升Office测试.docx",fetchUrl":"https://bisheng-upload.nodoc.cn/system/毕升Office免费私有化部署手册.docx" },"user":{"uid":"admin","nickName":"管理员","avatar":"https://bisheng-upload.nodoc.cn/system/defaultAvatar.png"}}

第二步对字符串进行base64编码得到值：

eyJkb2MiOnsiZG9jSWQiOiJiaXNoZW5nb2ZmaWNlIiwidGl0bGUiOiLmr5XljYdPZmZpY2XmtYvor5UuZG9jeCIsImZldGNoVXJsIjoiaHR0cHM6Ly9iaXNoZW5nLXVwbG9hZC5ub2RvYy5jbi9zeXN0ZW0v5q-V5Y2HT2ZmaWNl5YWN6LS556eB5pyJ5YyW6YOo572y5omL5YaMLmRvY3gifSwidXNlciI6eyJ1aWQiOiJhZG1pbiIsIm5pY2tOYW1lIjoi566h55CG5ZGYIiwiYXZhdGFyIjoiaHR0cHM6Ly9iaXNoZW5nLXVwbG9hZC5ub2RvYy5jbi9zeXN0ZW0vZGVmYXVsdEF2YXRhci5wbmcifX0=

因此最后得到文件预览的地址为：

http://**bisheng_host**/apps/editor/openPreview?data=eyJkb2MiOnsiZG9jSWQiOiJiaXNoZW5nb2ZmaWNlIiwidGl0bGUiOiLmr5XljYdPZmZpY2XmtYvor5UuZG9jeCIsImZldGNoVXJsIjoiaHR0cHM6Ly9iaXNoZW5nLXVwbG9hZC5ub2RvYy5jbi9zeXN0ZW0v5q-V5Y2HT2ZmaWNl5YWN6LS556eB5pyJ5YyW6YOo572y5omL5YaMLmRvY3gifSwidXNlciI6eyJ1aWQiOiJhZG1pbiIsIm5pY2tOYW1lIjoi566h55CG5ZGYIiwiYXZhdGFyIjoiaHR0cHM6Ly9iaXNoZW5nLXVwbG9hZC5ub2RvYy5jbi9zeXN0ZW0vZGVmYXVsdEF2YXRhci5wbmcifX0=

在浏览器中打开这个地址时，毕升Office将会调用相应的服务进行相应的处理。

同理可以得到编辑文件时相应的地址。

### 相关错误代码

该错误表示callURL值在office服务器端解码错误

```shell
{"code": "request data error",  "data": data, "error": err}
```

该错误表示Office服务器在请求集成方获取文件预览/编辑数据时发生错误。错误提示中的callurl为服务器请求发送错误的是地址。error为错误详细信息。

## 集成毕升Office方法二：使用callURL来传递数据

使用该方法集成可以传递更多的参数信息，适合比较复杂的应用场景。在集成Office时需要传递各种高级的参数时建议使用这种集成方式。在集成时，集成方需要实现一个http Get请求以返回毕升Office所需要的数据。在使用本方法对Office对文件进行编辑或者预览的地址为：

编辑时调用的链接为：http://**bisheng_host**/apps/editor/openEditor?callURL=XXX

预览时调用的链接为：http://**bisheng_host**/apps/editor/openPreview?callURL=XXX

其中callURL是集成方实现的http Get服务请求地址的base64编码。

例如：在我们提供的demo示例中，该callURL对应的原始地址为http://demohost/api/fileAcl/bishengoffice/admin ； 该地址返回的数据如下：

```json
{
    doc: {
       docId: "bishengoffice", 
       title: "毕升Office测试.docx",
       fetchUrl: "https://bisheng-upload.nodoc.cn/system/毕升Office免费私有化部署手册.docx"
    },
    user: {
       uid: "admin",
       nickName: "管理员",
       avatar: "https://bisheng-upload.nodoc.cn/system/defaultAvatar.png"
     },
}
```



首先：对请求地址http://demohost/api/fileAcl/bishengoffice/admin 进行base64编码，得到的结果为：aHR0cDovL2RlbW9ob3N0L2FwaS9maWxlQWNsL2Jpc2hlbmdvZmZpY2UvYWRtaW4=

因此得到的预览地址为：

http://**bisheng_host**/apps/editor/openEditor?callURL=aHR0cDovL2RlbW9ob3N0L2FwaS9maWxlQWNsL2Jpc2hlbmdvZmZpY2UvYWRtaW4=

以上流程可以用如下图描述（签名验证在用户开启签名验证后发生，具体参考[集成配置文档](integrate-config.html#开启签名验证))

![集成流程图](https://bisheng-public.nodoc.cn/resource/集成流程图.jpg)

### 集成方需要实现的 http Get请求的说明：

1. 该请求为get请求。
2. 该请求返回的数据格式为本文中第一部分规定的json数据格式。
3. 该请求的地址具体形式由集成方决定，只需要按照要求返回具体值即可。
4. <span style="color:red">callURL为该地址的base64编码结果</span>

### 相关错误代码

```json
{"code": "decode data  error"}
```

data/callurl base64解码错误。

```shell
{"code": "request callURL error", "callURL": callURL,"error":""}
```

该错误表示Office服务器在请求集成方获取文件预览/编辑数据时发生错误。错误提示中的callurl为服务器请求发送错误的是地址。error为错误详细信息。



## 文件回存

文件回存接口是文件编辑完成之后生成了新的文件或者其他原因生成了新的版本文件，毕升Office系统会将新文件通过回存接口发送到用户的系统。回存接口是集成方需要实现的。具体参考链接：[文档回存](integrate-savback.html)

## 集成涉及的其他配置

本文档主要介绍集成中的基本情况。集成中的其他配置信息参考[集成配置文档](integrate-config.html)。

## 集成demo

集成demo参考中介绍了集成毕升Office的基本过程。如果有需要请参考[集成demo文档](integrate-demo.html)
