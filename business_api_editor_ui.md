title: 毕升Office自定义UI
author: 毕升Office
tags:

  - API
categories:
  - 功能接口
abbrlink: integrate-ui-api
date: 2020-01-01 22:07:00
keywords: 自定义UI，状态控制，自定义UI菜单
description: 毕升Office自定UI，自定义裁定
---
@[toc]

本文将介绍如何通过配置文件或者调用参数来实现在对Office编辑/预览上的自定义。该配置属于非免费功能，在使用该功能时请确保你部署的毕升Office拥有相应的权限。

## 可以配置定义那些UI

 毕升目前提供的UI配置项包括：

1. 去掉编辑带背景色的横栏，即一级菜单入口。在这种情况下用户需要自行设计一级菜的UI，并且通过调用毕升office提供的接口来实现在编辑器中一级菜单的切换。
2. “文件”，“开始”，”插入“，”协同“，"布局"，“引用”六个（execl,ppt为三个）一级菜单的是否显示，一级菜单的名称（例如可以配置协同菜单为审核）；
3. 去掉某些一级菜单下的子菜单，可去掉项包括：文件下面的 保存，打印，导出； 协同菜单下面接受，拒绝，评论，强制修订处于打开模式。
4. 定义编辑区/预览区打开时的默认缩放比例。或者是将编辑/预览区宽度定义为自适应的。

## 如何实现定义UI

在集成毕升off ice时可以通过两种方式达到自定义UI的效果：

1. 通过配置文件来定义全局的UI。在用户在配置文件中按照毕升Office提供的配置格式来实现对编辑器UI的定义。**这些配置是对全部打开编辑的编辑器生效的（可以被API参数覆盖）**。
2. 通过API参数来实现对当前打开的编辑的UI的定义。这种方式只对当前打开的编辑器生效。并且会覆盖配置文件中的定义。这种方式常用在根据用户身份，文档状态等来实现对UI的控制。一个最常见的应用场景是和OA中的流程进行结合。例如：OA中现在规定文件只能被修订，且只有一部分人能接受/拒绝修订，其他人只能以强制打开修订的模式进行修改文件。此时需要使用这种方式来定义UI，在打开文件进行编辑时，**集成方需要传递不同的参数来控制编辑器的UI状态**

### 在配置文件中的自定义UI

在配置文件中定义UI需要按照如下格式在配置文件中（/bisheng_data/workspace/config/config.yml）添加如下配置。

```yaml
uiconfig:
  defineribbon: false   # 是否使用毕升Office默认一级菜单入口
  hiddentoolbar: false  # 是否隐藏全部工具栏菜单
  editorzonezoom: ""  # 打开编辑器时的缩放比例，字符串，如果为100%，则传入"100"，如果为"fit"，则宽度自适应容器
  viewerzonezoom: ""  # 打开预览时的缩放比例，字符串，如果为100%，则传入"100"，如果为"fit"，则宽度自适应容器
  pdfviewerzoom: ""  # 打开pdf预览时的缩放比例，字符串，如果为100%，则传入"1"，其它适应参数，"auto", "page-actual", "page-fit", "page-width"
  disablelogo: false  #关闭logo
  defaulttab: home  #定义打开编辑时默认打开菜单项，默认值为home，表示默认显示开始菜单。
  selectedtext:
    enable: false   #是否打开选中文字时的右键菜单。默认是false
    items:          #菜单数组
    - title: 回调一  #菜单上显示的内容
      key: call1    #该菜单的key，回调时会传递回去
    - title: 回调二
      key: call2
  files:                #定义文件菜单
    disable: false         #是否关闭文件一级菜单
    title: 文件             #文件菜单的标题
    subui:                 # 关闭文件菜单下的一些功能入口
      save:  false              #是否关闭保持按钮
      print: false              #是否关闭打印按钮   
      export: false             #是否关闭导出按钮
      about: false              #关闭about按钮
      rename: false             #禁用修改文件名称
  start:
    disable: false
    title: 开始
  insert:
    disable: false
    title: 插入
    sub_ui:
    	headerandfooter: false
    	contentcontrol: false
  reference:
    disable: false
    title: "引用"
    sub_ui:
    	toc: false 			#禁止目录入口
    	updatetoc: faslse   #禁止更新目录入口
    	footnote: false
    	bookmark: false  	
  collaboration:
    disable: false
    title: 协同
    subui:
      accept: false
      reject: false
      comments: false
    opentrackchange: false
    trackchangemodeinpreview: ""    # 预览状态修订显示模式(final, original, 空)
    trackchangemodeineditor: ""     # 编辑状态修订显示模式(final, original, 空)
    checkcomments: uid #批注的操作条件，可以为空。为uid时表示：只有本人可以删除，修改自己的批注
	checkreviews: uid  #修订的操作条件，可以为空。为uid时，表示只有本人可以删除，接受自己的修订
	layout:
    disable: false
    title: 布局
    subui:
      printarea: false #是否禁用打印区域，默认值不禁止
```

以上配置系统默认配置数据。如果对某一些部分进行自定义，自需要把该部分的配置加到配置文件中即可。例如我自需要定义协同菜单：需要把“协同”的名称改成“审核”，强制打开审核模式,同时不需要评论功能。则我的配置可以如下：

```yaml
uiconfig:
  collaboration:
    title: 审核
    subui:
        comments: true
    opentrackchange: true
```

![image-20190720154533901](https://bisheng-public.nodoc.cn/resource/image-20190720154533901.png)



配置完之后，重启服务之后，UI将是：

![image-20190720160454544](https://bisheng-public.nodoc.cn/resource/image-20190720160454544.png)

### 在调用参数中指定UI配置

调用参数中指定UI配置是指在集成方响应callurl时在“user” 数据下的opts key下返回UI配置。该UI配置完成的数据结构如下（json格式）：

```json
{
  "define_ribbon": false,   // 隐藏Title条
  "hide_toolbar": false,    // 隐藏所有工具条菜单
  "default_tab": "home",     // 定义打开编辑时默认打开菜单项，默认值为home，表示默认显示开始菜单。
  "editor_zone_zoom": "",    // 打开编辑器时的缩放比例，字符串，如果为100%，则传入"100"，如果为"fit"，则宽度自适应容器
  "viewer_zone_zoom": "",    // 打开预览时的缩放比例，字符串，如果为100%，则传入"100"，如果为"fit"，则宽度自适应容器
  "pdf_viewer_zoom": "",    // 打开pdf预览时的缩放比例，字符串，如果为100%，则传入"1"，其它适应参数，"auto", "page-actual", "page-fit", "page-width"
  "disable_logo": false,   //关闭logo
  "selected_text":{        //通过右键获取被选中的文字内容，默认不配置
     enable: false,
     items: [{
       title:"回调一",
       key:"call1"
     },{
       title:"回调二",
       key:"call2"
     }]         //菜单数组
  },
  "files":{
    disable: false,
    title: "文件"
    sub_ui：{
     	save: false,
      print: false,
      export: false，
      about:false,
      rename: false
    }
   },
   "start":{
      disable: false,
      title: "开始"
   }，
   "insert":{
      disable: false,
      title: "插入",
      sub_ui:{
        headerAndFooter: false,
        contentControl: false 
      }
   }，
	"reference":{
 				disable: false,
      	title: "引用"
        sub_ui: {
    		toc:false,
            updateToc:false,
            footnote: false,
            bookmark: false,
    	}
    },
    "layout":{
		 		disable: false,
         title: "布局",
         sub_ui: {
    				print_area:false,
          }
    },
   "collaboration"：{
       disable: false,
       title: "协同"，
       sub_ui: {
     	 	  accept: false,
          reject: false,
          comments: false,
      },
      open_track_change: false, // 是否用户编辑时强制开启修订模式输入
      track_change_mode_in_preview: '', // 预览状态修订显示模式(final, original, 空)
      track_change_mode_in_editor: '', // 编辑状态修订显示模式(final, original, 空)
      check_comments: uid,
	  	check_reviews: uid,
   }
}
```

在[毕升OfficeAPI集成说明](https://bishengoffice.com/apps/blog/posts/onlyoffice-api.html)中，callURL返回值的结构为：

```json
{
  doc:{},
  user:{
    ....
    opts:{
      "uiconfig":{}//自定义UI    
    }
  }
}
```

在使用调用参数来自定义UI时，需要使用到 user下的opts： 在opts下增加key "uiconfig"，值为自定义UI的数据。例如同样对于上面的例子：需要把“协同”的名称改成“审核”，强制打开审核模式,同时不需要评论功能。如果使用调用参数来实现，callurl返回值如下：

![image-20190720211](https://bisheng-public.nodoc.cn/resource/image-20190720211027144.png)

### 配置文件定义UI与参数定义UI的关系

首先需要明确的是：配置文件定义UI是全局的，会影响到word,excel,ppt编辑器；而参数定义UI只会影响到某一次打开编辑器。如果对于某个一级菜单的定义，参数方式会覆盖配置文件

## 隐藏一级菜单入口

对于部分用户集成时为了使得UI能够和系统进行更好的融合，需要自定义一级菜单入口。这是用户可以使用上面配置文件（调用参数）中的defineribbon（json格式为define_ribbon）；当这个值为true时，毕升的一级菜单将被隐藏。效果图如下：

![image-20190720165052908](https://bisheng-public.nodoc.cn/resource/image-20190720165052908.png)

此时，用户可以通过个API来实现各个一级菜单的转跳：例如用户需要从上图中的菜单进入到文件菜单，则需要调用API： window.bisheng_switchtab("file")

![image-20190720165331106](https://bisheng-public.nodoc.cn/resource/image-20190720165331106.png)

下面是进入文件，开始，插入，协同，数据五个菜单的API调用方式：

进入文件菜单： window.bisheng_switchtab("file")

进入开始菜单： window.bisheng_switchtab("home")

进入插入菜单：window.bisheng_switchtab("ins")

进入协同菜单：window.bisheng_switchtab("collab")

进入布局单：window.bisheng_switchtab("layout")

进入数引用单：window.bisheng_switchtab("ref")

当配置设定defaulttab值时，编辑器打开时将默认显示相应的菜单。

## 右键中获取word中选中的内容

通过配置中的selectedtext可以在右键菜单中增加一项来获取word中被选中的内容，并且定义相应的处理函数可以进行用户自定义的处理。

![image-20190802213350165](https://bisheng-public.nodoc.cn/resource/image-20190802213350165.png)

### 定义处理函数：

当鼠标点击新增的菜单项时，将会调用相应的处理过程。

1. 用户可以在window对象下定义函数selTxtCallback(content)，该函数将会在点击菜单项时被调用。

2. 当毕升Office是在iframe中使用时，需要在父frame中捕获message事件，当点击菜单项时，会将内容发送到上层frame中。

   ```javascript
   window.addEventListener('message', e=>{
         let data = e.data;
         console.info(data); //data的内容为{command: "SelectContent",content: ""} 
   });
   ```

​        毕升Office发送到父frame中的消息都是通过command来区分，command为 SelectContent时，表示该消息是选择word内容并通过右键菜单主动触发的，其中content为文本的内容。



## 定义预览/编辑区域缩放比例

在上面的配置项说明中通过editorzonezoom，viewerzonezoom，pdfviewerzoom三个配置来实现编辑模式，预览模式，以及PDF预览的缩放比例。

editorzonezoom/viewerzonezoom配置可选值有：

1. 大于零的数值，使用数值时表示编辑/预览默认打开时相对于标准大小的百分比（例如word大部分情况为A4宽度）。例如为100时，表示使用标准宽度，120表示1.2倍宽度。使用数值设置值不影响编辑器右下方缩放UI功能。

2. 字符串 "fit"。当配置的值为fit时，表示编辑/预览区域宽度为自适应的。缩放会随着编辑器可用区域的变化而自动变化。此时编辑器右下方缩放UI将不再出现。

例如下图时配置为120时编辑区宽度，并且编辑区不随走它空用区域（拉大缩小浏览器）的影响。

![image-20190918183111863](https://bisheng-public.nodoc.cn/resource/image-20190918183111863.png)

当配置为fit时，如下图下图：

![image-20190918183224212](https://bisheng-public.nodoc.cn/resource/image-20190918183224212.png)
