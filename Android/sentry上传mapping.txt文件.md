# sentry上传mapping文件

# 首先看官方文档:

https://docs.sentry.io/platforms/android/proguard/





有gradle插件可直接使用

然而按文档配置gradle插件后发现,根本没有用.

去查看[插件源码](https://github.com/getsentry/sentry-android-gradle-plugin/blob/master/src/main/groovy/io/sentry/android/gradle/SentryPlugin.groovy),发现这个gradle插件依赖命令行工具, 需要预先安装sentry-cli命令行,没有就无法生效.



![image-20210412143551811](https://gitee.com/hss012489/picbed/raw/master/picgo/1618209357498-image-20210412143551811.jpg)

![image-20210412143818477](https://gitee.com/hss012489/picbed/raw/master/picgo/1618209498503-image-20210412143818477.jpg)

一个gradle插件生效还要先手动安装命令行工具,也真TM蛋疼.

>  直接去命令行先看看是个什么鬼: 无非就是个文件上传请求而已:

命令行工具的安装:https://docs.sentry.io/product/cli/installation/

mapping文件上传的命令:https://docs.sentry.io/product/cli/dif/#proguard-mapping-upload

工具安装好后:

需要在.bash_profile里配置环境变量:

![image-20210412144150772](https://gitee.com/hss012489/picbed/raw/master/picgo/1618209710796-image-20210412144150772.jpg)

sentry-cli info 命令查看sentry的环境变量配置.



然后运行:

```c
sentry-cli  upload-proguard /Users/工程目录/app/build/outputs/mapping/release/mapping.txt --org org名 --project project名  --log-level=debug
```

然后可以看到:

会自动上传mapping和符号表:

![image-20210412145252093](https://gitee.com/hss012489/picbed/raw/master/picgo/1618210372118-image-20210412145252093.jpg)

![image-20210412145132560](https://gitee.com/hss012489/picbed/raw/master/picgo/1618210292585-image-20210412145132560.jpg)



可以看到权限校验模式为: Authorization: Bearer Token

> 那么,自己写个gradle插件,直接使用okhttp来执行网络请求即可.

在sentry界面上可以看到:

![image-20210412145614184](https://gitee.com/hss012489/picbed/raw/master/picgo/1618210574211-image-20210412145614184.jpg)



> 遗留问题: 怎么给mapping设置版本?或者说mapping文件怎么和实际exception关联?

通过uuid:

gradle插件上传mapping时,把文件uuid写到一个properties里,打包到apk中.

具体写properties的代码在cli里,但从读文件的地方可以看到写的是什么,可自己实现写的方法.

对应的完整命令行:

```c
sentry-cli upload-proguard \
    --android-manifest app/build/intermediates/manifests/full/release/AndroidManifest.xml \
    --write-properties app/build/intermediates/assets/release/sentry-debug-meta.properties \
    app/build/outputs/mapping/release/mapping.txt
```

那么这个apk每次上报sentry时会带上这个uuid,就可以关联到对应的mapping文件:

![image-20210412151042068](https://gitee.com/hss012489/picbed/raw/master/picgo/1618211442099-image-20210412151042068.jpg)



![image-20210412151405894](https://gitee.com/hss012489/picbed/raw/master/picgo/1618211645925-image-20210412151405894.jpg)