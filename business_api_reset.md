title: 毕升Office自定义UI
author: 毕升Office
tags:

  - API
categories:
  - 功能接口
abbrlink: integrate-rest-api
date: 2020-01-01 22:10:00
keywords: 删除文件，移除协同者，主动回存，查询状态，拷贝文件，文件预处理
description: 毕升Office相关API
---
@[toc]
本文将介绍商业版包含的API以及这些API应用的场景。这些API属于服务器端reset调用API，与编辑相关API不在本文中介绍。在使用商业版API之前需要先确认你部署的毕升office是否具备相应的权限。或者联系官方咨询相关信息。

## API安全验证

<p style="color:red">需要注意的是： URL中的参数 hashId值为 docId的md5哈希值api url 均为相对于毕升Office部署服务器的相对地址。在实际使用时，需要根据实际情况使用完成http(s)地址</p>

<p style="font-weight: bold">同时需要注意：如果开启了<a href="integrate-config.html#毕升office请求签名">签名验证</a>，则需要在 http 请求head里面如下添加两个值：1. time , 值为你发起请求是的时间，时间格式必须为ISO 8601格式；2. 使用 apikey对上面time的值进行HMAC-MD5签名的签名值。如下图</p>

![image-20200217202625632](<https://bisheng-public.nodoc.cn/resource/image-20200217202625632.png>)

## 删除文件

当你集成了毕升Office之后，如果你的系统中的文件被删除了，你可以通知毕升Office来关闭改文件的编辑。此时你可以调用这个API；

该API的地址为：/apps/editor/deleteFile/docId?hashId=xxx  其中 docId为该文档的id, 该API被调用之后，将会通知正在编辑这个文件的所有参与者，文件被删除了，必须关闭编辑；如果此时没有人在编辑这个文件，则什么也不会发生。

注意：**该API为post请求**；

该 API返回值为json对象，内容如下：

如果发生错误该则返回如下：

```json
{
  "status": false,
}
```

否则返回值如下：

```json
{
  "status": true,
}
```

## 移除协同编辑者

移除协同编辑者的API主要用于一下场景：在你的系统，你移除了某些人的权限，或者你取消了这个文件的共享。这个时候需要调用这个API来让参与编辑某个人或者所有人都退出编辑。该API有两种形式，**且都为post请求**：

1. /apps/editor/removeParticipant/docId?hashId=xxx 

2. /apps/editor/removeAllParticipant/docId?hashId=xxx  

   这两种形式的API中的 docId为文档的ID，

   第一个将移除某一些协同编辑者；post请求的参数body为一个用户ID的数组，该数组中的用户都是将被移除的协同编辑者。

   第二个将移除所有的协同编辑者，让所有参与该文档编辑的用户退出，然后从源文件处打开编辑。

   

该 API返回值为json对象，内容如下：

如果发生错误该则返回如下：

```json
{
  "status": false,
}
```

否则返回值如下：

```json
{
  "status": true,
}
```

##  文件重命名

该API是双向的，即：1. 集成方调用。当业务系统的中文名发生了变化时，主动调用通知毕升Office服务器进行相应的处理。如果此时该文件处于编辑状态，毕升Office服务器则会通知所有的编辑端进行相应的处理；2. 毕升Office编辑器端对文件名进行了更改，通过回调的方式通过业务系统进行相应的处理。

### 集成方调用重命名API

这种方式下 API的URL为：/apps/editor/rename/:docId?hashId=xxx；其中 :docId 为被重命名文件的ID。URL中的参数 hashId值为 docId的md5哈希值。该请求为 post请求，其中body的内容格式如下

```json
{
  "newName":"",
  "from":{
    "uid":""
  }
}
```

其中： newName 为该文件的新名称（需要保持文件后缀名）；from参数表明该操作是有哪个用户发起的，其中uid为该用户的ID（下面的api, from 表示的意义相同）

毕升Office服务器在收到请求之后作出相应的处理，如果该文件在编辑会通知编辑端进行相应的处理。处理完成之后，毕升Office服务器会返回相应的结果，返回结果内容如下：

```json
{
  "status": false/true,
}
```

Status 表明这次处理是否完成，处理正常则返回true,否则返回false

下图为毕升Office服务器收到重命名请求之后作出处理的效果。

![image-20190515170035296](https://bisheng-public.oss-cn-zhangjiakou.aliyuncs.com/resource/image-20190515170035296.png)

### 毕升Office文件重命名回调通知集成方

毕升office的编辑器中也对文件进行重命名。重命名之后，会通过回调方式通知业务系统进行相应的处理。其中回调的地址和回调回存的地址相同，即在/bisheng_data/workspace/config/config.yml配置的postapiurl 所指向的地址。

![image-20190515170948467](https://bisheng-public.oss-cn-zhangjiakou.aliyuncs.com/resource/image-20190515170948467.png)

**需要注意的是：postapiurl中的地址是一个post请求。在实现请注意**

该post请求的body内容如下格式：

```json
{
  "docId":"",
  "action":"",
  "data":{
       
  }
}
```

其中docId是当前文档的ID，action是当前回调的是什么类型。例如文件重命名回调时action的值为：rename。data值和action的值相关。重命名时data的数据格式如下：

```json
{
  "newName": name,
	"from":  {
    uid:""
  },
}
```

newName 为该文件重命名后的新名称，from和上文中意义相同，表明该操作是有哪个用户发起的，其中uid为该用户的ID。业务系统收到请求之后进行相应的处理，需要返回处理的结果，结果数据如下：、

```json
{
  "status": false/true,
}
```

Status 表明这次处理是否完成，处理正常则返回true,否则返回false

## 文件主动回存

该API主要是由业务系统主动要求毕升Office将编辑完的文件回存到业务系统。毕升Office在所有的编辑参与者退出编辑之后1分钟（可以配置）之后，会主动将该文件更新后的结果重新以Office文件的格式回存到业务系统。但是在一些情况下，业务系统要求立刻回存，则此时可以调用这个API。

API的URL为：/apps/editor/saveBack/:docId?hashId=xxx；其中 :docId 为被重命名文件的ID。URL中的参数 hashId值为 docId的md5哈希值。该请求为 post请求，其中body为空

毕升Office服务器在收到请求之后作出相应的处理，返回结果内容如下：

```json
{
  "status": false/true,
  "version"：{
  	
  },
  "reason":""
}
```

Status 表明这次处理是否完成，处理正常则返回true,否则返回false。

其中当stauts为true时，version内容不为空，version表示此次主动要求回存得到的新内容。

version 数据结构意义和文档回存时post到集成方系统中数据中的data部分时一致的：[见链接](integrate-savback.html#回存服务发送的数据结构)

当status为false时，reason会返回本次主动要求回存失败的原因。主要值有两类：1.failed,此时由于某些原因生成新的文件失败。2. locked，该文件还有人在编辑，因此放弃生成新文件。

<u>**特殊参数**</u>: 主动回存的API url 参数除 hashId是必须之外，还支持两个可选参数：nopost，posturl以及force。

nopost，posturl这两个参数的意义是：nopost为true 指定在用户调用主动回存时，不触发系统的回存调用，此时数据不通过回存服务发送到集成方系统。nopost 为false时，主动回存将会触发系统回存调用，此时回存调用会将数据发送到posturl所在的地址。该参数适用的应用场景是：对于从一个正在编辑的文件拷贝生成的新文件，此时还未打开过，如果需要主动回存同时需要指定回存地址，可以使用该参数。<span>除此之外的其他应用场景不建议使用。</span>

Force 参数的意义是：不管文件现在是否有人打开，都强制回存。如果此时有其他用户打开了文档，则其他用户会收到文件版本发生改变的信息。

## 文件预处理

预处理API主要的应用场景是：当业务系统中新产生**Office**文件时，可以调用该API，该API将返回Office文件的缩略图，提取该文件中的文本内容

API的URL为：/apps/editor/preConvert?hashId=xxx；其中 :docId 为被重命名文件的ID。URL中的参数 hashId值为 docId的md5哈希值。该请求为 post请求，其中body内容格式如下：

```json
{
  docId: "", 
  title: "",
  fetchUrl: "",    
}
```

docId: 文档的ID，不同的文档通过docId 来区分。

 title: 文件的标题，用于编辑时在编辑器上显示当前文件的标题，**需包含加上文件扩展名**

 fetchUrl: 该文件的获取链接。当毕升Office打开该文件时，会通过这个链接去获取文件。**该链接不要进行编码**

毕升Office服务器在收到请求之后会对传入的文档进行预处理，生成文件的缩略图以及提取文字内容；

```json
{
  "status": false/true,
  "png": "", 
  "text": ""
}
```

status 表明这次处理是否完成，处理正常则返回true,否则返回false。为true时，png,text分别是该文件的缩略图以及提取的文字内容

## 查询文档是否是打开状态

该API的作用是查询文档是否在毕升Office系统是打开的状态。API的url 地址为：/apps/editor/isOpening:docId?hashId=xxx 

docId 为文档的Id,hashId 为docId的md5哈希值。该API返回的值为：

```json
{"status": true, "isOpening": false}
```

Status: 本次调用是否正常返回；isOpening:  该文档是否是打开状态

## 拷贝正在编辑的文件

毕升Office提供API拷贝正在打开编辑的问题。该API解决的问题是：在集成系统中拷贝文件时，如果文件此时是处于编辑状体，并且没有将文件数据回存到集成系统，拷贝的文件将会是旧内容。

该API的URL地址是：/apps/editor/copyOnWrite/:from/:to  API参数意义是：from为 被拷贝的文档的docId;to新文档的docId。该API执行完成之后返回的数据如下：

```json
{"status": true}
```

Status为true,表示拷贝文件成功。

该API执行完成之后，在毕升Office系统中，新文档包括了被拷贝文件的最新的内容。<span style="color:red">此时如果集成系统需要获取最新的文件，需要调用一次主动回存的API即可。</span>

## 查询文档指纹

该API的URL地址为： /apps/editor/fingerprint/:docId。 文档指纹主要是反应文档是否被修改，如果文档被修改（哪怕是修改之后使用撤销），则文档的指纹将发生改变。使用该API时，集成方需要首先做一次查询得到第一次的指纹，在验证文档是否被更改时，再查询文档指纹，将两次的值进行对比即可。返回的数据如下：

```shell
{"status": true/false, "fingerprint": ""}
```

## 查询文件的历史版本

该API的URL地址为： /apps/editor/version/:docId/list 。该API将返回该文件所以的历史版本以及历史版本对应的下载地址。返回的数据如下：

```shell
{"status": true/false, "versions": []}
```

