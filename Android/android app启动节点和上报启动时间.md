# Android app启动节点和上报启动时间

# app启动的关键节点

> 经常利用content provider 和Androidx里的 startup库来对库进行初始化操作,那么app启动关键方法的执行顺序是什么样的呢? 怎么样控制我的库的启动顺序?

参考这篇文章: [Android 多个 ContentProvider 初始化顺序](https://sivanliu.github.io/2017/12/16/provider%E5%88%9D%E5%A7%8B%E5%8C%96/)

精髓在这张图里:

![image-20220427201057606](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac/1651061457655-image-20220427201057606.jpg)

> 回答上面的问题: 怎么样控制我的库的启动顺序?

推荐用contentprovider,设置initOrder. 

不推荐用startup,因为它只能在dependices()回调里通过依赖来控制,是强依赖,不够灵活

![image-20220427202324777](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac/1651062204810-image-20220427202324777.jpg)

# 启动时间怎么算

### 方案1: 参考firebase: 

>  从第一个contentProvider的attachInfo,到第一个页面的onReusme:

[app-start-foreground-background-traces](https://firebase.google.com/docs/perf-mon/app-start-foreground-background-traces?authuser=0&platform=android)

```tex
App start trace
This trace measures the time between when the user opens the app and when the app is responsive. In the console, the trace's name is _app_start. The collected metric for this trace is "duration".

Starts when the app's FirebasePerfProvider ContentProvider completes its onCreate method.

Stops when the first activity's onResume() method is called.

Note that if the app was not cold-started by an activity (for example, by a service or broadcast receiver), no trace is generated.
```

看一下FirebasePerfProvider的配置:

initOrder="101",基本是最大的. 项目里其他的Provider都没有怎么配置initOrder

```xml
    <provider
            android:name="com.google.firebase.perf.provider.FirebasePerfProvider"
            android:authorities="${applicationId}.firebaseperfprovider"
            android:exported="false"
            android:initOrder="101" />
```

可以自己搞个类似的trace打印/上报一下

![image-20220427201630165](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac/1651061790197-image-20220427201630165.jpg)

```xml
<provider
            android:name="com.xxx.logs.AppStartMeasurer"
            android:authorities="${applicationId}.AppStartMeasurer"
            android:exported="false"
            android:initOrder="102" />
```

然后就可以看logcat的日志输出+ trace平台的统计了

## 方案2 : ams

adb 命令:

```shell
adb shell am start -W 包名/入口activity全类名
```

在控制台会输出日志:

![image-20220427202057491](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac/1651062057526-image-20220427202057491.jpg)

> 这里的时间会比方案1统计到的时间小一些