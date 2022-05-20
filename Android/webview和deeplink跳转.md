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





# 如何实现adjust那样的一个https链接自动识别是否安装app,自动跳转的功能

> 关键在于如何让外部任意一个浏览器都能帮我们探测是否安装目标app

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

