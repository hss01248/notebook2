# webview和deeplink跳转



# demo工程

https://github.com/hss01248/deeplinkDemo

# deeplink接收

参考 [Android 上玩转 DeepLink：如何最大程度的向 App 引流](https://juejin.cn/post/6844903620287135752)

建一个activity,贴入:

```xml
<activity
            android:name=".RouterActivty"
            android:exported="true"
            android:theme="@style/Theme.AppCompat.Light.NoActionBar">
            <intent-filter android:autoVerify="true">
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data
                    android:host="news.zhoulujue.com"
                    android:pathPattern="/.*"
                    android:scheme="http" />
                <data
                    android:host="news.zhoulujue.com"
                    android:pathPattern="/.*"
                    android:scheme="https" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="zljnews" />
                <data android:host="zljnews" />
                <data android:pathPattern="/.*" />
            </intent-filter>
        </activity>
```

 activity里接收入参:

```java
public class RouterActivty extends AppCompatActivity {

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        AlertDialog dialog = new AlertDialog.Builder(this)
                .setTitle("router")
                .setMessage(getIntent().getDataString())//拿到uri
                .setPositiveButton("ok", new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialog, int which) {
                        finish();

                    }
                }).create();
        dialog.show();
    }
}
```



# webview对deeplink的处理

参考:[Android WebView 跳转第三方App](https://blog.csdn.net/Jaden_hool/article/details/60873044)

> 重写shouldOverrideUrlLoading + 处理onReceivedError回调里的errorCode == WebViewClient.ERROR_UNSUPPORTED_SCHEME的情况.
>
> 因为有的不走shouldOverrideUrlLoading

处理方式:

```java
public static void jump(Context context,String newurl){
        //弹窗选择:
        //if (!newurl.startsWith("http")) { 这个判断在shouldOverrideUrlLoading里加
            try {
                final Intent intent = new Intent(Intent.ACTION_VIEW, Uri.parse(newurl));
                //List<ResolveInfo> resolveInfos = context.getPackageManager().queryIntentActivities(intent, 0);
                ResolveInfo resolveInfo = context.getPackageManager().resolveActivity(intent, 0);
                if(resolveInfo != null){
                    String name = "";
                    if(resolveInfo.activityInfo != null && !TextUtils.isEmpty(resolveInfo.activityInfo.packageName)){
                        name = AppUtils.getAppName(resolveInfo.activityInfo.packageName);
                    }
                    XLogUtil2.w(resolveInfo, name);
                    AlertDialog dialog = new AlertDialog.Builder(context)
                            .setTitle("跳转")
                            .setMessage("是否跳转到 "+ name)
                            .setPositiveButton("ok", new DialogInterface.OnClickListener() {
                                @Override
                                public void onClick(DialogInterface dialog, int which) {
                                    intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK
                                            | Intent.FLAG_ACTIVITY_SINGLE_TOP);
                                    context.startActivity(intent);
                                }
                            }).setNegativeButton("cancel", new DialogInterface.OnClickListener() {
                                @Override
                                public void onClick(DialogInterface dialog, int which) {

                                }
                            }).create();
                    dialog.setCancelable(false);
                    dialog.setCanceledOnTouchOutside(false);
                    dialog.show();
                }else {
                    //引导跳到应用市场
                    ToastUtils.showLong("没有对应的activity,跳去应用市场吧");
                }

            } catch (Exception e) {
                // 防止没有安装的情况
                XLogUtil.exception(e);
                ToastUtils.showLong("没有对应的activity,跳去应用市场吧");
                //No Activity found to handle Intent { act=android.intent.action.VIEW
                // dat=tunaikumobile://open?_branch_referrer=H4sIAAAAAAAAA8soKSkottLXLynNS8zMLtVLLCjQy8nMy9Z3zC7NScwuDYGIG9mrGpkYF9gmxidCxAHgNy05OAAAAA==&link_click_id=991275456483771349 flg=0x30000000 }

                //{ act=android.intent.action.VIEW dat=intent:// flg=0x30000000 }

            }

        //}
    }
```



## 对intent://的处理

注意,如果在shouldOverrideUrlLoading里不处理,直接调用super.shouldOverrideUrlLoading(),那么会没有翻译,系统webview默认不处理,而不是默认处理.

### 方式1:

完全手动

分析intent://的格式,手动截字符串

```html
<a href="intent://zljnews/recipe/100390954#Intent;scheme=zljnews;package=com.zhoulujue.news;end"> Intent scheme </a> 
```

解析

```java
 /**
     * chrome不会帮我们处理,构建系统intent也找不到对应的activity,需要自己改成标准格式: zljnews://news.zhoulujue.com/recipe/100390954
     * @param context
     * @param newurl
     * @return
     */
    private static boolean jumpByIntent(Context context,String newurl) {
        int idx = newurl.indexOf("#");
        if(idx<0){
            LogUtils.w("非法intent,不带#",newurl);
            return false;
        }
        String path0 = newurl.substring(0,idx);
        String next = newurl.substring(idx+1);
        if(!next.startsWith("Intent;") || !next.endsWith(";end")){
            LogUtils.w("非法intent,不以end结尾",newurl);
            return false;
        }
        Map<String,String> map = new HashMap<>();

        String[] strings = next.split(";");
        for (String string : strings) {
            if(string.contains("=")){
                String[] split = string.split("=");
                map.put(split[0], URLDecoder.decode(split[1]));
            }
        }
        if(map.containsKey("scheme") && map.containsKey("package")){
            String realUrl = path0.replace("intent://",map.get("scheme")+"://");
            LogUtils.w("realUrl",realUrl,map);
            final Intent intent = new Intent(Intent.ACTION_VIEW, Uri.parse(realUrl));
            ResolveInfo resolveInfo = context.getPackageManager().resolveActivity(intent, 0);
            if(resolveInfo == null){
                ToastUtils.showLong("没有对应的activity,跳去应用市场吧: "+ map.get("package"));
                return false;
            }
            return jump(context, realUrl);
        }
        LogUtils.w("非法intent,不含scheme 和 package",newurl);
        return false;
    }
```





### 方式2:

利用Intent.URI_INTENT_SCHEME

```java
private static boolean jumpByIntent2(Context context, String newurl) {
        Intent intent = null;
        try {
            //使用Intent.URI_INTENT_SCHEME,表示适配url为intent://开头的情况
            intent = Intent.parseUri(newurl, Intent.URI_INTENT_SCHEME);
            intent.addCategory(Intent.CATEGORY_BROWSABLE);

            intent.setComponent(null);
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.ICE_CREAM_SANDWICH_MR1) {
                intent.setSelector(null);
            }
            ResolveInfo resolveInfo = context.getPackageManager().resolveActivity(intent, 0);
            if(resolveInfo == null){
                ToastUtils.showLong("没有对应的activity,跳去应用市场吧: " );//map.get("package")
                return false;
            }

            //todo 弹窗提示
            ActivityStackManager.getInstance().getTopActivity().startActivityIfNeeded(intent, -1);
            //return jump(context, realUrl);
        } catch (Exception e) {
            e.printStackTrace();
        }

        return false;
    }
```





# 测试

## 自定义协议

zljnews://zljnews/article/123456/

![image-20220429160114417](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac/1651219280010-image-20220429160114417.jpg)

![image-20220429160138980](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac/1651219299024-image-20220429160138980.jpg)

## http协议

触发404或者dns未解析: errorCode == WebViewClient.ERROR_HOST_LOOKUP

![image-20220429160422138](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac/1651219462183-image-20220429160422138.jpg)

![image-20220429160445484](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac/1651219485526-image-20220429160445484.jpg)



# 关于webview拦截的回调

* 直接用webvie.loadUrl(url), 走的是shouldInterceptRequest()  判断是否 main frame
* ,如果是web页面里点击链接,则走shouldOverrideUrlLoading

> 

# 关于一个h5页面在webview里加载耗时的计算

>  同理,页面加载耗时,其开始的判定,应该统一由shouldInterceptRequest() 里开始计时,而不是由onPageStart

使用下面的库来观测:

```groovy
api 'com.github.skyNet2017:webviewdebug:1.2.2-from117'
```

## 1 初次进入,使用webvie.loadUrl(url):

shouldInterceptRequest()->onPageStarted()->onPageFinished() 

其中onPageFinished() 可能回调多次,onProgressChanged()  的100进度也可能回调多次.

![image-20220520100200416](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac2/1653012120544-image-20220520100200416.jpg)

## 2 在里面点击某个链接

shouldOverrideUrlLoading()->shouldInterceptRequest()->onPageStarted()->onPageFinished()

其中onPageFinished() 可能回调多次,onProgressChanged()  的100进度也可能回调多次

![image-20220520100334728](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac2/1653012214774-image-20220520100334728.jpg)

## 3 重定向的页面

# 2 一些问题

## 2.1 为什么扫码直接访问applink或intent开头的link无法跳转,而把这些link放到web页面点击打开就能跳?

如下,intent://和zljnews://的链接,直接在各种浏览器输入框输入,或者生成二维码扫码进入,都无法直接唤起app

但是把它们放到一个网页中,则用浏览器都可以点击打开?

```html
<br/>
<a href="intent://news.zhoulujue.com/recipe/100390954#Intent;scheme=zljnews;package=com.hss.deeplinktargetapp;end"> chrome规定的Intent scheme-> intent://news.zhoulujue.com/ </a>
<br/>
<br/>
<a href="zljnews://news.zhoulujue.com/article/123456/"> zljnews://news.zhoulujue.com </a>
<br/>
```

> 原因是因为新打开webview时,访问的url不会走shouldOverrideUrlLoading(). 而在web页面内点击链接,则会走.
>
> 一般浏览器在实现对 applink或intent开头的link的处理都是只处理shouldOverrideUrlLoading(),不会去额外处理shouldInterceptRequest().
>
> 我们自己实现webview时,应参考这个惯例,只在shouldOverrideUrlLoading()里处理deeplink相关问题.

那么还有问题,新打开webview访问deeplink,因为没有走shouldOverrideUrlLoading(),最终必然会走到webviewClient的onReceiveError里,如果项目真的要对此种情况特殊处理,可以在这个回调进行:

```java
   if (errorCode == WebViewClient.ERROR_UNSUPPORTED_SCHEME) {
     //类似shouldOverrideUrlLoading()的操作
   }
```



## 2.2 如何实现adjust那样的一个https链接自动识别是否安装app,自动跳转的功能

> 关键在于如何让外部任意一个浏览器都能帮我们探测是否安装目标app
>
> 只需要将https指向一个会web地址,在该页面上,先发一个延时3s重定向到google play的任务,
>
> 然后立刻进行window.location.href=intent://xxxx.

利用chrome的intent://功能

```html
js里怎么跳app: 本身的zljnews://zljnews/article/123456/,用js写是:
<a href="intent://zljnews/recipe/100390954#Intent;scheme=zljnews;package=com.zhoulujue.news;end"> Intent scheme </a>  点击后,chrome会将url转义?
参考  https://developer.chrome.com/docs/multidevice/android/intents/
```

但需要各浏览器本身都适配intent://的协议,且在没有响应activity时跳转market://包名,甚至识别adjust的特有fallback url

```tex
intent://xxx.yyy.com?actionKey=DzAAAAAuuAA=&adjust_reftag=cmPkr9gOrmOOK#Intent;scheme=zzz;package=io.zzzz.uuuu;S.market_referrer=adjust_reftag%3DcmPkr9gOrmOOK;S.browser_fallback_url=https%3A%2F%2Fplay.google.com%2Fstore%2Fapps%2Fdetails%3Fid%3Dio.zzzz.uuuu%26referrer%3Dadjust_reftag%253DcmPkr9gOrimOOK;end

需要解析并转换成真正的deeplink:

│ args[0] = realUrl
│ args[1] = zzz://xxx.yyy.com?actionKey=DzAAAAAuuAA=&adjust_reftag=cmPkr9gOrmOOK
│ args[2] = {package=io.zzzz.uuuu, S.browser_fallback_url=https://play.google.com/store/apps/details?id=io.zzzz.uuuu&referrer=adjust_reftag%3DcmPkr9giOrmOOK, scheme=zzz, S.market_referrer=adjust_reftag=cmPkr9gOrmOOK}
```

## 2.3 google play的延迟深度链接怎么做?

Facebook和adjust , appsFlyer,branchIO均具有延迟深度链接的功能.

延迟深度链接的本质是从点击链接开始,如果没有安装该app,则跳转google play并携带一个特定参数给google play, google play在用户点击安装并打开这个app后,会将该参数传入到app中.

这个参数是: referrer

比如一个adjust的广告链接,在没有安装app时,跳入google play:

```txt
https://play.google.com/store/apps/details?id=com.xxx.yyy&referrer=adjust_reftag%3DctdsesfDFRE3Z%26utm_source%3D%25E9%2582%25AE%25E4%25BB%25B6%25E8%2590%25A5%25E9%2594%2580%25E8%25BF%25BD%25E8%25B8%25AA%25E9%2593%25BE%25E6%258E%25A5
```

第一次打开app内接收referrer参数,可以手动接收,

也可以使用谷歌的sdk: [Google Play Install Referrer](https://developer.android.com/google/play/installreferrer)

此sdk能提供一些广告相关的额外信息:

![image-20220526102525204](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac2/1653531930890-image-20220526102525204.jpg)



[android developers-处理 Android 应用链接](https://developer.android.com/training/app-links)

[adjust-深入了解移动应用深度链接](https://www.adjust.com/zh/blog/dive-into-deeplinking/)

[为应用广告添加深度链接](https://developers.facebook.com/docs/app-ads/deep-linking?locale=zh_CN)

[App深度链接与延迟深度链接](https://www.biaodianfu.com/deep-link-deferred-deeplink.html)
