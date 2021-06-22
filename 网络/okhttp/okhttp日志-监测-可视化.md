# okhttp日志,监测,可视化

# 拦截器提供的功能和拓展

两个接口:

* interceptor

* eventListener

  

  待拓展

  进度显示

  上传/下载的文件的meta信息,比如图片exif信息,保存路径信息等等.

  应用拦截器层的请求/响应体信息和网络拦截器层的请求响应体信息(自动解gzip)

  



# 一些参考

## 纯log

https://github.com/Ayvytr/OKHttpLogInterceptor

https://github.com/ihsanbal/LoggingInterceptor

https://github.com/square/okhttp/tree/master/okhttp-logging-interceptor/src/main/kotlin/okhttp3/logging

## app内可视化

[OkNetworkMonitor](https://github.com/linkaipeng/OkNetworkMonitor)

![image-20210610093314474](https://gitee.com/hss012489/picbed/raw/master/picgo/1623288799662-image-20210610093314474.jpg)

![image-20210610093332986](https://gitee.com/hss012489/picbed/raw/master/picgo/1623288813014-image-20210610093332986.jpg)

https://github.com/jgilfelt/chuck

https://github.com/ChuckerTeam/chucker



https://github.com/raskasa/metrics-okhttp



## pc上可视化

flipper

## 进度

https://github.com/JessYanCoding/ProgressManager





# frida脚本

https://github.com/siyujie/OkHttpLogger-Frida





https://github.com/InflationX/ViewPump