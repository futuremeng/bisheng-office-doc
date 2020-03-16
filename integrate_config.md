title: 毕升Office集成配置
author: 毕升Office
tags:
  - integrate
categories:
  - 集成
abbrlink: integrate-config
date: 2020-01-01 22:23:00
keywords: 集成开启签名验证 集成时用户权限 配置使用PDF预览 为文档指定回存地址
description: 毕升Office集成相关配置
---
@[toc]

本文将介绍在集成，调用毕升Office过程中使用到的配置项。前文关于毕升Office集成前文的介绍都是使用默认的配置项，如果这些默认的配置项不满足你的要求，你可以阅读本文，在实际使用过程中选择你需要的配置项。

<span style="color:red">注意：如果要修改之后的配置文件生效，需要服务重启方式为：执行restart.sh 脚本。</span>

## 毕升Office请求签名

默认情况下，在集成毕升Office 时对请求的数据是不需要签名的。如果你把毕升Office部署在内网，默认配置是没有问题的。但是如果你是部署在外网，为了防止未经授权的调用，你需要开启毕升Office的签名验证功能，并且在集成调用毕升Office时加上签名参数。

该配置项是基于系统安全角度的考虑，不是必须，用户根据自己的实际情况决定是否采用。

### 开启签名验证

修改配置文件毕升office配置文件 /bisheng_data/workspace/config/config.yml，在配置文件中增加配置项：

```yaml
apis:
   urlsign: true
```

修改毕升Office的配置之后，需要重启服务。

### 请求参数加上签名值

在开启签名验证功能之后，调用毕升Office时需要加上签名数据，具体如下：。

#### 开启签名之后的第一种集成方式

以第一种调用方式为例：在启用了签名配置之后，直接传递数据集成毕升Office的地址将是：

预览文件的URL：http://**bisheng_host**/apps/editor/openPreview?data=XXX&sign=XXX

编辑文件的URL：http://**bisheng_host**/apps/editor/openEditor?data=XXX&sign=XXX

<span style="color:red">其中sign参数值是使用HMAC-MD5对data的值进行签名的结果</span>，

#### 开启签名之后的第二种集成方式

如果你是使用第二种方式集成毕升Office，则开启签名配置之后，请求地址将变成：

编辑时调用的链接为：http://**bisheng_host**/apps/editor/openEditor?callURL=XXX&sign=XXX

预览时调用的链接为：http://**bisheng_host**/apps/editor/openPreview?callURL=XXX&sign=XXX

<span style="color:red">其中sign参数值是使用HMAC-MD5对callURL的值进行签名的结果</span>。具体HMAC-MD5算法意义和上文中第一种集成调用时一致的。

#### 关于hmac-md5以及api key

HMAC-MD5相关介绍请自行查阅相关资料。 Hmac-md5签名需要使用的key为从控制台（/apps/console）得到的 api key（如下图）。例如45ae1f8b5d50ea9322a3d8e3326ca0f9是我们的api key, 我们以nodejs 为例对字符串 bishengoffice.com进行签名：

```javascript
var crypto = require('crypto');
var hmac = crypto.createHmac('md5', '45ae1f8b5d50ea9322a3d8e3326ca0f9'); //第二个参数为api key
data = hmac.update('bishengoffice.com');
gen_hmac= data.digest('hex');
console.log("hmac : " + gen_hmac);
==========================输出为：
hmac : a3bcad4f42b7b41edbf7f737bd4d8254  //签名结果
```

在实际的开发中，需要根据具体开发语言实现相应HMAC-MD5 ，并且可以使用上面例子中的数据来验证实现是否有问题。

需要注意的是：启用了签名配置之后，如果调用过程中没有传递签名参数，或者签名结果不正确，将导致调用过程失败。错误代码：

```json
{"code": "check callURL sign error"}
```

下图在控制台（/apps/console）的许可信息中获取api key：

![image-20190325153100624](https://bisheng-public.oss-cn-zhangjiakou.aliyuncs.com/resource/image-20190325153100624.png)

<span style="color:red">另外需要强调的是：当打开签名验证之后，所有对毕升Office进行系统调用API时必须在http head里面加上请求时间（时间格式必须为ISO 8601），以及使用apikey对时间进行HMAC-MD5签名的签名值。</span>



## 文件打开的权限

在默认情况下，集成调用毕升Office时不需要特别指定用户打开这个文件的权限。毕升Office默认用户本次操作拥有毕升Office编辑器支持的全部权限，这些权限包括：

FILE_READ：可读，用户可以预览该文件，

FILE_WRITE：可编辑，用户可以编辑该文件；

FILE_DOWNLOAD：可下载，用户可下载该文件；

FILE_PRINT：可打印，用户可以打印该文件。

如果默认权限在实际过程中过大，则需要在调用时增加权限参数。如下时包含了权限参数(privilege数组)之后调用毕升Office的json格式数据：

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
       avatar: "https://bisheng-upload.nodoc.cn/system/defaultAvatar.png",
       privilege: [
         'FILE_READ','FILE_WRITE','FILE_DOWNLOAD', 'FILE_PRINT' 
       ]
     },
}
```

在实际使用过程中更加实际使用需要可以减少部分权限：例如预览模式下：如果是在预览模式下，需要隐藏编辑以及下载按钮，可以不传FILE_WRITE和FILED_OWNLOAD这两个权限值。在编辑模式下，如果禁止文件下载，可以不传入FILE_DOWNLOAD参数。

## 使用PDF方式预览文件

默认模式Office文件的预览都是使用毕升Office原生预览的。office 原生预览的优势是效果好，能做到和打开编辑是一致的。但是不足之处在于，速度稍慢。因此毕升Office还提供pdf预览模式。

需要PDF预览时可以在doc的opts指定pdf_viewer为true。指定PDF预览的数据结构如下：

```json
{
    doc: {
       docId: "bishengoffice", 
       title: "毕升Office测试.docx",
       fetchUrl: "https://bisheng-upload.nodoc.cn/system/毕升Office免费私有化部署手册.docx",
       opts:{
         pdf_viewer:true  // 指定该文档的预览方式为PDF预览
       }
    },
    user: {
       uid: "admin",
       nickName: "管理员",
       avatar: "https://bisheng-upload.nodoc.cn/system/defaultAvatar.png"
     },
}
```

上述在doc的opts指定PDF预览产生的效果是：所有用户预览该文档时，都是使用PDF预览。如果在实际使用过程中，只想指定特定用户预览时采用PDF预览，则可以在在该用户的数据 use的opts制定PDF预览。具体数据如下：

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
       avatar: "https://bisheng-upload.nodoc.cn/system/defaultAvatar.png",
       opts:{
         pdf_viewer:true  // 指定该文档的预览方式为PDF预览
       }
     },
}
```

## 为每个文档指定回存的地址

在前面集成毕升Office的文档中，文档回存地址需要在配置文件中指定。这种指定的结果是，所以的文档的回存都会调用这个一个地址。如果在实际使用的过程中存在不同的文档需要向不同的地址进行回存，则可以在调用时为文档指定该文档的回存地址。指定方式是在doc数据中指定callback值。具体的数据结构如下：

```json
{
    doc: {
       docId: "bishengoffice", 
       title: "毕升Office测试.docx",
       fetchUrl: "https://bisheng-upload.nodoc.cn/system/毕升Office免费私有化部署手册.docx",
       callback:"https://bishengoffice.com/apps/drive" //指定该文档的回存地址
    },
    user: {
       ....
     },
}
```

当文档数据指定callback之后，该文档的回存都会发送到callback指定的地址。







