# webview和deeplink跳转

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