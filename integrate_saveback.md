title: 毕升Office集成回存
author: 毕升Office
tags:
  - integrate
categories:
  - 集成
abbrlink: integrate-savback
date: 2020-01-01 22:21:00
keywords: 集成回存
description: 毕升Office集成回存
---
@[toc]

本文介绍毕升Office集成使用时的如何实现文档回存以及毕升Office集成几个常用的API

## 文档回存

在集成毕升Office之后，Office文件编辑的内容将作为中间数据保持在毕升Office服务器端的数据库中。在所用用户退出编辑，服务器在一段时间内（默认配置是1分钟）没有收到任何关于该文档的数据之后，会将中间数据写回到office文件中去，并且生成包含新内容的office文件。对于集成方，如果需要获得这些文件，需要实现一个<span style="color:red">**回存服务**</span>。

<span style="color:red">**回存服务:**</span>是集成方实现的一个**post请求**，该请求将会被毕升Office从服务器端调用，并且将新生产的Office文件以及其他的数据文件的下载地址发送到集成方回存服务。集成方的回存服务在收到这个post请求后，可以按照需要进行处理。

### 配置回存服务API地址：

编辑毕升Office配置文件（/bisheng_data/workspace/config/config.yml），在配置文件中增加如下如下内容：

```yaml
apis:
   postapiurl: http://192.168.2.66:9090/api/file/saveBack
```

![image-20190325223245235](https://bisheng-public.oss-cn-zhangjiakou.aliyuncs.com/resource/image-20190325223245235.png)

修改毕升Office的配置之后，需要重启毕升Office服务

```shell
bash restart.sh
```

### 回存服务发送的数据结构

回存服务是一个post请求，该请求body内容如下格式：

```json
{
  "docId":"",
  "action":"saveBack",
  "data":{
       
  }
}
```

其中docId是当前文档的ID，action指定了本次post来的数据是关于文档回存的。data的内容是本次文档回存的具体数据。此时的data的格式为：

```json
{
  "docId":"",
  "docURL":"",
  "docUrlEncode":"",
  "modifyBy":[{
    "uid":"",
    "oid":"",
    "avatar":"",
    "nickName":""
  }],
  unchanged: true/false,
  "pngUrl":"",
  "txtUrl":"",
  "txtUrlEncode":"",
  "pngUrlEncode":""
}
```

各部分key的意义是：

docId 是当前回存文档的ID，

docURL是修改完最新的文件的下载地址;

docUrlEncode: docURL的base64编码，用于用户对docURL进行校验

unchanged 是标示这个文件有没有被修改；

pngUrl这个文件最新的缩略图；

pngUrlEncode：pngUrl的base64编码，用于用户对pngUrl进行校验

txUrl 这个文件最新提取的文本; 

txtUrlEncode： txUrl的base64编码，用于用户对txUrl进行校验

modifyBy：这个文件在这次编辑过程中被哪些人修改过。: docURL的base64编码，用于用户对docURL

unchanged： 是标示这个文件有没有被修改；

pngUrl这个文件最新的缩略图；

pngUrlEncode：pngUrl的base64编码，用于用户对pngUrl进行校验

txUrl 这个文件最新提取的文本; 

txtUrlEncode： txUrl的base64编码，用于用户对txUrl进行校验

reportUrl: 文件中的批注，修订，以及脚注内容抽取组成的json文件。<span style="color:red">【只包含在付费版中】</span>

reportUrlEncode: reportUrl地址的base64编码，用于用户对reportUrl进行校验

modifyBy：这个文件在这次编辑过程中被哪些人修改过。

versionIdx： 当前回存文档的版本。

其中需要特别指出是时：为了便于集成方在开发过程中避免URL地址解码问题，在提供直接地址的同时，还提供了这些地址的base64编码值。集成方在使用直接地址进行文件下载文件有问题时，可以：1. 自行处理地址的编码问题。2. 解码该地址对应的base64编码值，使用解码后地址来替代原来地址下载文件。

<span style="color:red">注意这些都是相对于毕升Office所在服务器的相对地址，在使用时需要加上http(s): //IP(毕升Office域名)，同时这些地址有效期约60分钟。</span>

<span style="color:red">备注</span>：unchanged为true的情况下，表示该文档没有更改，此时docUrl 可能为空也可能为该文档原始地址，或者是该文档上一次更改产生的结果。此时集成方系统需要根据实际情况进行处理。官方建议是：可以更加docUrl的类型进行处理；可以不做任何处理。

### 回存服务调用时机：

有如下几种情况将会触发系统回存服务：

1. 所有的编辑参与者退出编辑，且在一段时间内（默认配置是1分钟）没有任何用户重新进入编辑；
2. 所有的编辑参与者退出编辑后，有其他用户打开文件进行预览（此时不管其他该文档结束编辑多长时间）；
3. 有一个用户在客户端出发了新版本的请求将；
4. 集成方服务器触发了主动回存API（商业版以上版本专用API）

### 回存数据类型配置

在默认情况下，Office服务端生成回存包含的文件类型只有office文件。如果需要包含pdf, 缩略图，文本内容以及批注，修订，脚注等内容的提出，可以安装如下格式修改配置文件（配置文件位于/bisheng_data/workspace/config/config.yml）。

```yaml
export:
  document:      #word 的导出项配置
    pdf:  false  #是否导出PDF，
    text: false  #是否导出文本内容
    png: false   #是否导出文件的缩略图
    report: false   #是否提取文档中批注，修订，脚注等内容
  excel:
  	pdf:  false  #是否导出PDF，
    csv: false  #是否导出csv格式
    png: false   #是否导出文件的缩略图
    report: false   #是否提取文档中批注
  presentation:
  	pdf:  false  #是否导出PDF，
    text: false  #是否导出text格式
    png: false   #是否导出文件的缩略图
    report: false   #是否提取文档中批注
		
```

以上配置项直接添加到/bisheng_data/workspace/config/config.yml中，并且把你需要的导出的数据类型对应的值改为true。
