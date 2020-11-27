# webview工程化

[TOC]



# 概述

## 预先阅读资料

>  webview最常见的一些问题

[Android开发：最全面、最易懂的Webview详解](https://www.jianshu.com/p/3c94ae673e2a)

 [最全面总结 Android WebView与 JS 的交互方式](https://www.jianshu.com/p/345f4d8a5cfa)

[手把手教你构建 Android WebView 的缓存机制 & 资源预加载方案](https://www.jianshu.com/p/5e7075f4875f)

 [你不知道的 Android WebView 使用漏洞](https://www.jianshu.com/p/3a345d27cd42)

## 玩好webview的前提

> 了解一点web最最基础的知识

### html DOM树模型s

> vs Android的view树

![DOM HTML tree](https://www.runoob.com/images/htmltree.gif)

![image-20201112094944686](https://gitee.com/hss012489/picbed/raw/master/picgo/1605145789884-image-20201112094944686.jpg)

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

## 示例

全属性版:

```javascript
 Set-Cookie：customer=huangxp; id=a3fWa; path=/foo; domain=.ibm.com; 
 Expires=Wed, 21 Oct 2015 07:28:00 GMT; Secure; HttpOnly
```

最简化版:

```javascript
Set-Cookie: key1=value2; domain=example.com;

Cookie: name=value; name2=value2; name3=value3
```



## cookie规范-RFC 6265

https://github.com/renaesop/blog/issues/4

https://javascript.ruanyifeng.com/bom/cookie.html

要点:

* 域名和path的匹配: 一级域名,二级域名....
* 在请求头和响应头里的字段: cookie:xxx    Set-Cookie:xxx
* 几个属性: Domain,path,  Expires，Max-Age,Secure; HttpOnly

## 注入native网络框架来的cookie

> native通过cookie或token维护登录态,那么打开webview以及登录态变更时,需同步到webview中

* 安全顾虑->配置白名单

* 随意修改domain: 常见的同一个公司多个一级域名共用token.在构建注入到webview的cookie时,将cookie的domain设置为目标域名的domain,如此才能注入成功.

  ```java
  public void addCookieFromNetworkFrame(String host, CookieManager cookieManager) {
          List<Cookie> cookies = getAllCookie();
          boolean isInWhite = isInWhiteList(host);//判断是否在白名单里
          StringBuilder sb = new StringBuilder();
          for (Cookie cookie : cookies) {
              sb.delete(0, sb.length());
              sb.append(cookie.name()).append("=").append(cookie.value());
              if(isInWhite){
                  sb.append(";Domain=").append(host);//域名在白名单里,就给这个域名注入
              }else {
                  sb.append(";Domain=").append(cookie.domain());//否则,保持原来的域名. 
                //如果host和cookie.domain()不一致,此操作最后cookieManager.setCookie相当于无效
              }
              //.append(";Path=").append(cookie.getPath());不要再限定path.
             cookieManager.setCookie(host, sb.toString());
          }
      }
  ```

  

  

## 注入通用cookie

> 一些没有安全顾虑的数据,随意设置即可.

```java

cookieManager.setCookie(host, name + "=" + value +";Domain=" + host);
```



# websettings

> webview的配置. 需要定制比较多,很多常用功能都是默认关闭的

### 基本配置

```java
WebSettings mWebSettings = webView.getSettings();
mWebSettings.setDefaultTextEncodingName("utf-8");//字符编码UTF-8
//支持获取手势焦点，输入用户名、密码或其他
webView.requestFocusFromTouch();

mWebSettings.setSupportZoom(false);//不支持缩放
mWebSettings.setBuiltInZoomControls(false);


//设置自适应屏幕，两者合用
mWebSettings.setLoadWithOverviewMode(true); // 缩放至屏幕的大小
mWebSettings.setUseWideViewPort(true); //将图片调整到适合webView的大小

mWebSettings.setNeedInitialFocus(true); //当webView调用requestFocus时为webView设置节点

mWebSettings.setLoadsImagesAutomatically(true);
mWebSettings.setBlockNetworkImage(false);
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
   //适配5.0不允许http和https混合使用情况
    mWebSettings.setMixedContentMode(WebSettings.MIXED_CONTENT_ALWAYS_ALLOW);
}
mWebSettings.setEnableSmoothTransition(true);
webView.setFitsSystemWindows(true);
mWebSettings.setRenderPriority(WebSettings.RenderPriority.HIGH);//提高渲染等级


mWebSettings.setSavePassword(false);
// 允许加载本地文件html  file协议
mWebSettings.setAllowFileAccess(true);

//mWebSettings.setTextZoom(100);
mWebSettings.setDefaultFontSize(16);
        mWebSettings.setMinimumFontSize(12);//设置 WebView 支持的最小字体大小，默认为 8
        mWebSettings.setGeolocationEnabled(true);
//UserAgent
 mWebSettings.setUserAgentString(getWebSettings()
                .getUserAgentString()
                .concat(USERAGENT_AGENTWEB)
                .concat(USERAGENT_UC)
```

### js配置

```java
//调用JS方法.安卓版本大于17,加上注解 @JavascriptInterface
mWebSettings.setJavaScriptEnabled(true);//支持javascript
webView.addJavascriptInterface(Html5JsObj.create(webView), TAG);
```



### 多窗口配置

```java
//多窗口支持-打开才能收到onCreateWindow()回调
mWebSettings.setJavaScriptCanOpenWindowsAutomatically(true);//支持通过js打开新的窗口
mWebSettings.setSupportMultipleWindows(true);
```

### 缓存配置

```java
if (NetStatusUtil.isConnected(webView.getContext())) {
    mWebSettings.setCacheMode(WebSettings.LOAD_DEFAULT);//根据cache-control决定是否从网络上取数据。
} else {
    mWebSettings.setCacheMode(WebSettings.LOAD_CACHE_ELSE_NETWORK);//没网，则从本地获取，即离线加载
}

mWebSettings.setAllowFileAccess(true);
mWebSettings.setSaveFormData(true);
mWebSettings.setDomStorageEnabled(true);
mWebSettings.setDatabaseEnabled(true);
mWebSettings.setAppCacheEnabled(true);
String appCachePath = webView.getContext().getCacheDir().getAbsolutePath();
mWebSettings.setAppCachePath(appCachePath);
//缓存文件最大值
        mWebSettings.setAppCacheMaxSize(Long.MAX_VALUE);
```

# webviewclient

![image-20201111113817733](https://gitee.com/hss012489/picbed/raw/master/picgo/1605065897842-image-20201111113817733.jpg)

# webchromeclient

> chrome内核对js 浏览器模型的一些方法响应回调.

![image-20201111114007053](https://gitee.com/hss012489/picbed/raw/master/picgo/1605066007161-image-20201111114007053.jpg)

# 几个关键事件的回调

## 重定向: 301,302..

shouldOverideUrlLoading回调里,request.isRedirect=true时.

## 页面开始

onPageStart或shouldOverideUrlLoading

## 页面加载完成

onPageFinished/onProgress(100)

## 页面错误的兼容

[WebView：onReceiveError的应用与变迁](https://blog.csdn.net/wjr1949/article/details/72312283)

### input file文件选择适配

暂时限定只选择图片或调用系统拍照功能

//用到库: implementation('com.github.skyNet2017:TakePhoto:4.2.1')

```java
 @Override
                    public boolean onShowFileChooser(WebView webView, ValueCallback<Uri[]> filePathCallback, FileChooserParams fileChooserParams) {
                        TakePhotoUtil.startPickOne(AgentWebActivity.this, true, new TakeOnePhotoListener() {
                            @Override
                            public void onSuccess(String path) {
                                Log.d(DebugWebChromeClientLogger.DEFAULT_TAG,"onShowFileChooser-path:"+path);
                                Uri[] uris = new Uri[1];
                                uris[0] = Uri.fromFile(new File(path));
                                filePathCallback.onReceiveValue(uris);
                            }

                            @Override
                            public void onFail(String path, String msg) {
                                Log.d(DebugWebChromeClientLogger.DEFAULT_TAG,"onShowFileChooser-onFail:"+path);
                                filePathCallback.onReceiveValue(null);
                            }

                            @Override
                            public void onCancel() {
                                Log.d(DebugWebChromeClientLogger.DEFAULT_TAG,"onShowFileChooser-onCancel:");
                                filePathCallback.onReceiveValue(null);
                            }
                        });
                        return true;
                    }
```

```html
//html测试代码
<div>
    <label for="avatar">Choose image to upload</label>
    <input type="file"  id="avatar" name="avatar"
           accept="image/png, image/jpeg" onchange="function handleFiles(files) {
                var imgSrcI = getObjectURL(files[0]);
                  console.log('uri:'+imgSrcI)
               document.getElementById('avatar_img').src = imgSrcI;
               console.log('files:'+files[0].name)
                console.log('files:'+files[0].size)
                document.getElementById('avatar_txt').innerText = 'uri is : '+imgSrcI+', \n'+(files[0].size/1024).toFixed(2)+'kB';
                console.log('files:'+files[0].text())
                console.log('files:'+files[0].stream.toString())


           }
           handleFiles(this.files)"/>
</div>
<div>
    <a id="avatar_txt"></a><br>
    <img id="avatar_img">
</div>
```





#### 测试结果

Log

![image-20201119192331382](https://gitee.com/hss012489/picbed/raw/master/picgo/1605785011462-image-20201119192331382.jpg)

![image-20201119191853544](https://gitee.com/hss012489/picbed/raw/master/picgo/1605784740154-image-20201119191853544.jpg)

## 



# js和native怎么相互调用,以及获取返回值

> 本质上就是java和javascript怎么通信



//chromclient回调处理:

## js调用native

* 官方途径:

```java
		webView.addJavascriptInterface(xxx)

 		@JavascriptInterface
    public void jump2TakePhoto(int model, String func) {
    ...
    }
//@JavascriptInterface注解的方法可以直接返回一个string或者数值类型,js侧可直接拿到返回值.
//也可使用下方native调用js的方式,让js方以回调的形式接收.
```

* 一些其他途径: 比如通过promt,alert的交互来

  https://github.com/lzyzsd/JsBridge   https://github.com/pedant/safe-java-js-webview-bridge

## native调用js

```java
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
    webview.evaluateJavascript("javascript:"+jscode, callback);//4.4以上,可以设置webview调用js方法后的回调.
} else {
    webview.loadUrl("javascript:"+jscode);
}
```



# js bridge

> 主要讲述addJavascriptInterface方式的交互

## 线程切换

> @JavascriptInterface注解的方法,js调用时是在webview内核的开的子线程中.需要在主线程的操作需自行切换线程.

## 与装载webview的具体activity/fragment解耦

**不要在具体activity里写js bridge的逻辑处理代码.**

持有webview和相应的activity,就可以做任何事情.->使用透明fragment代理activity的各种回调,比如权限,onactivityresult,生命周期等等.

也可用库:https://github.com/hss01248/StartActivityResult

![image-20201106112841306](https://gitee.com/hss012489/picbed/raw/master/picgo/1604633321359-image-20201106112841306.jpg)

## 免crash

>  @JavascriptInterface注解的方法内部发生崩溃会导致webview卡住或白屏,因此需要进行统一处理.
>
> 可使用aop对所有方法进行try-catch.
>
> 也可使用静态代理,如下方协议设计里所示,在WebProxyHandler内部统一处理crash的情况.

## 跨端协议设计

> 兼顾RN,webview使用回调的形式将数据回传给js.RNModule使用Promise将数据传递到RN
>
> 回传数据格式参考前后端交互的data-code-msg模式设置,序列化为json.



比如对接权限申请:

```java
//接口
public interface IPermissionRN {

    void checkPermissions(String paramsJson, String callbackName, Promise promise) throws Exception;
    }
//实现    
 public class PermissionImpl extends BaseCallbackBridgeImpl implements IPermissionRN {
    public PermissionImpl(WebView webView, AppCompatActivity activity) {
        super(webView, activity);
    }

    @Override
    public void checkPermissions(String paramsJson, String callbackName, Promise promise) throws Exception {   
      ....
    }
   
 //RN module:
public class PermissionModule extends ReactContextBaseJavaModule implements IPermissionRN {
    IPermissionRN proxy;
    public PermissionModule(@Nonnull ReactApplicationContext reactContext) {
        super(reactContext);
        proxy = new RnProxyHandler<IPermissionRN>(new PermissionImpl(null,null))
                .getProxy();
    }

    @Nonnull
    @Override
    public String getName() {
        return "PermissionModule";
    }
    @ReactMethod
    @Override
    public void checkPermissions(String paramsJson, String callbackName, Promise promise) throws Exception {
        proxy.checkPermissions(paramsJson, callbackName, promise);
    }
}
//web的javaObject:
    permissionProxy = new WebProxyHandler<IPermissionRN>(new PermissionImpl(webView,activity),webView).getProxy();
   
    @JavascriptInterface
    public void checkPermissions(String paramsJson, String callbackName) throws Exception {
        permissionProxy.checkPermissions(paramsJson,callbackName,null);
    }
```

## 文档和测试代码管理

> 文档

javadoc->html->部署到nginx,公司内可访问

> 测试代码

webstorm开发,本地测试通过后->gitlab->push触发webhook->jenkins自动构建并自动部署到nginx



# 通用ui设计和控制

## 页面栈管理

> 可以提供方法给js操纵后退行为.以及打开新activity行为
>
> Window.open打开的新页面可使用fragment承载,实现多窗口切换,也可简单粗暴弹出新activity

```java
@Override
public void onBackPressed() {
    if(webView != null && webView.canGoBack()){
        webView.goBack();
    }else {
        super.onBackPressed();
    }
}
```

## 进度条

> 参考微信. 必要时使用假进度,以安慰用户心理

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

> onProgress 100不一定走. onPageFinished可能会多次调用.

## 错误页面设计建议

> 参考UC.初始界面为友好提示界面,有按钮点击进入查看错误详情,以及点击上报错误信息功能.

## 新页面弹出的回调适配

```javascript
<button onclick="function openwin() {
      var win = window.open('https://www.baidu.com')
      setTimeout(function() {
        win.close()
      },5000)
      }
openwin()">windowopen打开百度,5s后自动关闭</button>
```

分别回调老webview的onCreateWindow()和新webview的chromClient的onCloseWindow().

onCreateWindow(): 构建新webview,处理好信息传递后,将新webview设置给新activity的contentview

onCloseWindow():新actiivty的webview响应此方法,处理好新activity的关闭.

```java
@Override
public boolean onCreateWindow(WebView view, boolean isDialog, boolean isUserGesture, Message resultMsg) {
    try {


        newWebView = webViewInit.initWebView(view.getContext(), new IReveiveTitle() {
            @Override
            public void onTitleReceived(String title) {
                if(tvTitle != null){
                    tvTitle.setText(title);
                }
            }
        });
        Log.w("webdebug", "onCreateWindow:isDialog:" + isDialog + ",isUserGesture:" + isUserGesture + ",msg:" + resultMsg + "\n chromeclient:" + this+","+newWebView);
        WebView.WebViewTransport transport = (WebView.WebViewTransport) resultMsg.obj;
        transport.setWebView(newWebView);//关联新老webview,传递信息
        resultMsg.sendToTarget();

        new Handler().post(new Runnable() {
            @Override
            public void run() {
                try {
                    View view1 = initView(view.getContext(), newWebView);
                    Activity activity = getActivityFromContext(view.getContext());
                    if(activity instanceof  AppCompatActivity){
                        AppCompatActivity compatActivity = (AppCompatActivity) activity;
                        StartActivityUtil.startActivity(compatActivity,
                                FullScreenActivity.class,FullScreenActivity.intent(compatActivity),
                                false, new TheActivityListener<FullScreenActivity>(){
                                    @Override
                                    protected void onActivityCreated(@NonNull FullScreenActivity activity, @Nullable Bundle savedInstanceState) {
                                        super.onActivityCreated(activity, savedInstanceState);
                                      
                                        activity.setContent(view1);//将新webview设置给新的activity的content
                                        activity.setWebView(newWebView);
                                        webViewInit.getImpl().fullScreenActivity = activity;


                                    }
                                });
                    }else {
                        Log.w("jsoprnwindow","not compactactivity:"+activity);
                    }


                }catch (Throwable throwable){
                    throwable.printStackTrace();
                }
            }
        });

        return true;
    } catch (Throwable e) {
        e.printStackTrace();
        try {
            WebView.WebViewTransport transport = (WebView.WebViewTransport) resultMsg.obj;
            transport.setWebView(view);
            resultMsg.sendToTarget();
        }catch (Throwable throwable){
            throwable.printStackTrace();
        }

        return true;
    }
}

public static Activity getActivityFromContext(Context context) {
    if (context == null) {
        return null;
    }
    if (context instanceof Activity) {
        return (Activity) context;
    }
    while (context instanceof ContextWrapper) {
        if (context instanceof Activity) {
            return (Activity) context;
        }
        context = ((ContextWrapper) context).getBaseContext();
    }
    return null;
}

private View initView(Context context, WebView webView) {
    LinearLayout root = (LinearLayout) LayoutInflater.from(context).inflate(R.layout.webview_open_container,webView,false);

     tvTitle= (TextView) root.findViewById(R.id.tv_title);
    //ImageView ivClose= (ImageView) root.findViewById(R.id.iv_web_close);
    LinearLayout llWeb= (LinearLayout) root.findViewById(R.id.web_ll_container2);
    LinearLayout.LayoutParams params = new LinearLayout.LayoutParams(LinearLayout.LayoutParams.MATCH_PARENT,LinearLayout.LayoutParams.MATCH_PARENT);
    webView.setLayoutParams(params);
    llWeb.addView(webView);
    return root;
}




/**
 * https://blog.csdn.net/asdfghgw/article/details/76066769
 * 此方法在新打开的页面里响应回调.
 * @param window
 */
@Override
public void onCloseWindow(WebView window) {
    Log.w("webdebug", "onCloseWindow:window:" +window+  this+","+fullScreenActivity+","+newWebView+",");
    if(fullScreenActivity != null){
        fullScreenActivity.finish();
        fullScreenActivity = null;
    }else {
        Activity activity = getActivityFromContext(window.getContext());
        if(activity != null){
            activity.finish();
        }
    }
    try {
        if (newWebView != null) {
            newWebView = null;
        }
    } catch (Throwable e) {
        e.printStackTrace();
    }
}
```

## Web控制页面ui显示的方式

* 通过url控制
* 通过cookie控制
* 通过js bridge控制
* 通过BOM api控制



# debug工具-可观测性

库: https://github.com/skyNet2017/webviewdebug/blob/master/README2.md

## chromeTools

chrome内核的pc浏览器上输入: chrome://inspect    Android 4.4以下,Android webview内核是webkit,而不是chromium,所以不可用.

需要设置:  

```java
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
    WebView.setWebContentsDebuggingEnabled(!Config.isReallyRelease());
}
```

## webviewclient和webchromeclient的各种回调

静态代理打印日志. 便于优化不同回调的个性化显示

![image-20201029194236926](https://camo.githubusercontent.com/629e38f474b53792777965dfd6c7f7c09fd084599f91ac5a455b8b2dba9e0ddf/68747470733a2f2f67697465652e636f6d2f6873733031323438392f7069636265642f7261772f6d61737465722f706963676f2f313630333937313735363937322d696d6167652d32303230313032393139343233363932362e6a7067)

## js bridge的交互: js调native,native调js的观测

动态代理打印日志,或者使用aspectj切入打印. 可使用专门给aspectj设计的日志库:'

https://github.com/hss01248/aop-android/tree/master/logforaop

```
api 'com.github.hss01248.aop-android:logforaop:1.0.1'
```

同时打印一份到浏览器的console上,让web同学不需要看logcat就能看到日志:

```java
public static void logInJsConsole(WebView webView,String str){
        webView.postDelayed(new Runnable() {
            @Override
            public void run() {
                String js = "javascript:console.log('"+str+"')";
                Log.w("js",js);
                webView.loadUrl(js);
            }
        },0);
    }
```



## 在任意界面嵌入js的debug面板

[eruda](https://github.com/liriliri/eruda/blob/master/doc/README_CN.md)

操作dom,嵌入eruda的script节点

注意需要在onpagefinished后操作dom

```java
static String fuc3 = "(function () { var script = document.createElement('script'); " +
        "script.src=\"https://cdn.jsdelivr.net/npm/eruda\"; " +
        "document.body.appendChild(script); " +
        "script.onload = function () { eruda.init() } })();";

public static void invokeJsDebugPan(WebView webView){
    webView.postDelayed(new Runnable() {
        @Override
        public void run() {
            String js = "javascript:"+fuc3;
            webView.loadUrl(js);
        }
    },0);
}
```

## 错误上报

# 安全

## https相关

### 如何在webview端进行证书校验

* 建立ssl连接时校验
* 证书被系统判定错误后再校验.-onReceivedSslError()



### google play关于https校验error的处理的要求

![img](https://gitee.com/hss012489/picbed/raw/master/picgo/1605148456978-Center.jpg)

谷歌不允许直接proceed,以下两种处理方式可任选其一:

* 弹窗让用户选择是否继续请求(推荐)
* 自己做证书校验.示例代码如下:



证书错误后,再校验

参考:[Android webview手动校验https证书（by 星空武哥）](https://blog.csdn.net/lsyz0021/article/details/54669914)

![image-20201112103602006](https://gitee.com/hss012489/picbed/raw/master/picgo/1605148562108-image-20201112103602006.jpg)

![image-20201112103647389](https://gitee.com/hss012489/picbed/raw/master/picgo/1605148607487-image-20201112103647389.jpg)

## cookie相关

## local storage相关



# 性能优化

## web页面加载基本流程

[web页面加载、解析、渲染过程](https://blog.csdn.net/weixin_30535843/article/details/96036521)

![img](https://gitee.com/hss012489/picbed/raw/master/picgo/1605153058090-825196-20170328150055436-1910402537.jpg)



## 加载时间优化

> 缓存文件,缓存webview对象

### 优化webview的缓存管理

系统缓存限制太大，实现缓存的主要方式是通过拦截webview的访问，然后实现自定义缓存，把这种实现方式封装成库
https://github.com/yale8848/CacheWebView

### web离线包: 变远程为本地

业界最常用的. 需要web端配合做差分包. Android下载后做差分合并,用到bsdiff.

基本原理:

复写shouldInterceptRequest方法,如果资源url对应的本地离线包文件存在,则读取本地文件,构建WebResourceResponse返回.

```
WebResourceResponse shouldInterceptRequest(WebView view,
        WebResourceRequest request)
        
WebResourceResponse shouldInterceptRequest(WebView view,
            String url)
```

### Android侧:延迟图片加载

在加载前先阻塞加载图片

```java
settings.setBlockNetworkImage(true);
```

在WebView 渲染完成后，解除阻塞，加载图片。WebViewClient中:

```java
public void onPageFinished(WebView view, String url) {
            settings.setBlockNetworkImage(false);
            //判断webview是否加载了，图片资源
            if (!settings.getLoadsImagesAutomatically()) {
                //设置wenView加载图片资源
                settings.setLoadsImagesAutomatically(true);
            }
            super.onPageFinished(view, url);
        }

```



### 预初始化webview

在Application预先初始化好WebView, 当第二次初始化WebView的时候速度就快多了

### web侧:优化代码

[web页面性能优化](https://blog.csdn.net/qq_41807645/article/details/80839757)

# 参考和扩展阅读

[Android开发：最全面、最易懂的Webview详解](https://www.jianshu.com/p/3c94ae673e2a)

 [最全面总结 Android WebView与 JS 的交互方式](https://www.jianshu.com/p/345f4d8a5cfa)

[手把手教你构建 Android WebView 的缓存机制 & 资源预加载方案](https://www.jianshu.com/p/5e7075f4875f)

 [你不知道的 Android WebView 使用漏洞](https://www.jianshu.com/p/3a345d27cd42)



[关于webview与H5属性设置以及交互的总结](https://blog.csdn.net/u011200604/article/details/52767304)

[Android Webview H5 秒开方案实现](https://mp.weixin.qq.com/s/XfBt_gTw0gN7tXzuyP4PTw)







