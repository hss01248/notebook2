# webview工程化

[TOC]



# 概述

## 玩好webview的前提

> 了解一点web最最基础的知识

### html DOM树模型s

![DOM HTML tree](https://www.runoob.com/images/htmltree.gif)

### JavaScript 浏览器对象模型   

参考 

 https://www.runoob.com/js/js-window.html

https://www.runoob.com/jsref/obj-window.html

![image-20201106103840040](https://gitee.com/hss012489/picbed/raw/master/picgo/1604630320077-image-20201106103840040.jpg)

BOM对象方法调用-> 触发WebChromeClient相关回调

Android webview的特点: 对浏览器对象的所有方法都是空实现,也就是需要自己实现

> BOM对象属性就不管了,最多给你们提供点方法的回调



# BOM模型相关的js方法

## window操作

- window.open() - 打开新窗口 ->  onCreateWindow(WebView view, boolean isDialog,
              boolean isUserGesture, Message resultMsg)
- window.close() - 关闭当前窗口  -> onCloseWindow(WebView window)
- window.moveTo() - 移动当前窗口
- window.resizeTo() - 调整当前窗口的尺寸

## 三种弹窗

https://www.runoob.com/js/js-popup.html

> alert("str")

> 类比Android上没有取消按钮的alert dialog

![image-20201106094055926](https://gitee.com/hss012489/picbed/raw/master/picgo/1604626856021-image-20201106094055926.jpg)

>  confirm("*sometext*") 

![image-20201106094215602](https://gitee.com/hss012489/picbed/raw/master/picgo/1604626935632-image-20201106094215602.jpg)

>  prompt("str")-接收用户的输入

![image-20201106094602922](https://gitee.com/hss012489/picbed/raw/master/picgo/1604627162954-image-20201106094602922.jpg)



## js里的logcat: 

console.log("str")  console.error(), warn, info



## 文件选择:file标签

```javascript
< input  type =  "file"  id  = "fileupload"  name =  "fileupload"  style =" display  : none " />
```



# cookie管理

## cookie规范-RFC 6265

https://github.com/renaesop/blog/issues/4

## 注入native网络框架来的cookie

* 安全顾虑-配置白名单
* 随意修改domain

## 注入通用cookie





# websettings

# webviewclient

# webchromeclient



# js和native怎么相互调用,以及获取返回值

## js调用native

## native调用js

```java
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
    webview.evaluateJs("javascript:"+jscode, callback);
} else {
    webview.loadJs("javascript:"+jscode);
}
```



# js bridge

## 线程切换



## 与装载webview的具体activity/fragment解耦

不要在具体activity里写js bridge的逻辑处理代码.

持有webview和相应的activity,就可以做任何事情.->使用透明fragment代理activity的各种回调,比如权限,onactivityresult,生命周期等等.

也可用库:https://github.com/hss01248/StartActivityResult

![image-20201106112841306](https://gitee.com/hss012489/picbed/raw/master/picgo/1604633321359-image-20201106112841306.jpg)

## 免crash

## 跨端协议设计

## 文档和测试代码管理

> 文档

javadoc->html->部署到nginx,公司内可访问

> 测试代码

webstorm->gitlab->push触发webhook->jenkins自动构建并自动部署到nginx



# 通用ui设计和控制

## 页面栈管理

## 进度条

## web页面加载完成的判定

https://blog.csdn.net/u012107143/article/details/100655182

#### 区分DOMContentLoaded事件和load事件

- DOMContentLoaded事件：
  The DOMContentLoaded event fires when the initial HTML document has been completely loaded and parsed, without waiting for stylesheets, images, and subframes to finish loading.
  对应$(document).ready(function() { // …代码… });
- load事件：
  The onload property of the GlobalEventHandlers mixin is an EventHandler that processes load events on a Window, XMLHttpRequest, <img> element, etc.
  对应$(document).load(function() { // …代码… });

所以：

1. 如果只是想操作dom元素，那么在ready函数里就可以进行了；
2. DOMContentLoaded事件标志着：html中的css都下载完了，js都下载和执行完了；但是并不保证页面已经被完整渲染。

#### onPageFinished和onProgress 100



## 错误页面设计建议

## 新页面弹出的回调适配

## Web控制页面ui显示的方式

* 通过url控制
* 通过cookie控制
* 通过js bridge控制
* 通过BOM api控制



# 可观测性

## webviewclient和webchromeclient的各种回调

静态代理打印日志

## js bridge的交互: js调native,native调js的观测

动态代理打印日志

## 在任意界面嵌入js的debug面板

[eruda](https://github.com/liriliri/eruda/blob/master/doc/README_CN.md)

操作dom,嵌入eruda的script节点

注意需要在onpagefinished后操作dom

## 错误上报

# 安全

## https相关

### 如何在webview端进行证书校验

### google play关于https校验error的处理的要求

## cookie相关

## local storage相关



# 性能优化

## web页面加载基本流程



## 首屏时间优化

### web离线包



## 滑动流畅度优化

