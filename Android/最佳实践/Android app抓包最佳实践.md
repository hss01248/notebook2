# Android app抓包最佳实践

# 抓自己开发的app的包

自己开发的APP的抓包调试,进行一系列的配置,让任何情况下都能抓包.

Mainfest.xml里配置networkSecurityConfig: debug时允许http明文+ 允许https证书链溯源至用户导入的证书.

> 如此配置则可以让电脑代理抓包或者手机vpn代理抓包能安装代理的证书,看到明文.
>
> 同时,如果app有证书锁定/公钥锁定功能,应在debug时关闭.(比如okhttp的)



```xml
android:networkSecurityConfig="@xml/my_net_config"

//对应配置文件如下 @xml/my_net_config:

```

```xml
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <debug-overrides >
        <trust-anchors>
            <certificates src="system"/>
            <certificates src="user"/>
        </trust-anchors>
    </debug-overrides>
  <!--false代表不允许明文http. 一般用于release发版. (比如谷歌商店政策,发现明文http配置会警告)
为了debug,可以在src/debug/res/xml下放置同名文件,cleartextTrafficPermitted改成true-->
    <base-config cleartextTrafficPermitted="false">
        <trust-anchors>
            <certificates src="system" />
        </trust-anchors>
    </base-config>
</network-security-config>
```

参考: 

https://developer.android.com/training/articles/security-config



证书锁定功能是指如下配置: 

或者自己在verifyHostName(), checkServeTrusted()等回调里拿到证书链自己实现的证书锁定.

```java
     String hostname = "publicobject.com";
     CertificatePinner certificatePinner = new CertificatePinner.Builder()
         .add(hostname, "sha256/AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA=")
         .build();
     OkHttpClient client = OkHttpClient.Builder()
         .certificatePinner(certificatePinner)
         .build();
```

## 代码侵入

### 纯logcat: 

添加为okhttp拦截器.

```groovy
implementation "com.squareup.okhttp3:logging-interceptor:3.12.12"
```

缺点: 

只能抓添加了这个拦截器的经过okhttp的包.无法抓urlconnection,httpclient的包.

#### 通知栏抓包

chuck,chucker之类的开源库, 也是基于okhttp拦截器. 同样的局限性.

https://github.com/ChuckerTeam/chucker

### flipper抓包

基于okhttp拦截器. 

加上aop,可抓全部okhttp,无需手动添加拦截器.

另外对图片上传增加了exif信息输出到header中.

优点: 界面大,copy方便

缺点: 只能显示okhttp的请求.

https://github.com/hss01248/flipperUtil

# 代理模式

> 独立,无代码侵入.

## 手机上的代理

基于vpnservice.  最出色的为httpcanary. 可以只看特定应用的请求. 

http://www.shensuxiazai.com/app/3631.html

## pc上的代理

mac上的chales, windows上的fiddler.

# 终极

如何抓第三方app的包?

其app开了证书锁定怎么办?

> 当然是上神器frida.  
>
> 写Android代码,打成dex,用frida加载注入. 把okhttp的相关证书锁定,校验功能全给关掉, 强制app裸奔. 然后就用上述的两种代理模式抓就行了.
>
> 难点在于手机需要root,或者手机不root,但需要重新打包apk.



> 或者使用frida直接hook,运行时修改networkConfig相关的系统类?

参考: https://blog.csdn.net/yuanguozhengjust/article/details/101215026