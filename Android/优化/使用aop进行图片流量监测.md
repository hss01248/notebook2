# 使用aop进行图片流量和缓存性能监测



# 图片流量整体走向和缓存架构

内存缓存bitmap

本地缓存小图----本地缓存大图

cdn缓存: resize,格式转换, 最大程度节省流量

服务器源图: 图片上传时选择合适的宽高,格式,压缩算法/工具





# 流量监测

## 客户端

关键在于代理所有网络请求

参考firebase-perf思路

或者httpcanary的vpn代理思路



实现功能:

统计

排序->单个url,某段时间内某个url加和

罗列异常图片

## cdn控制台

自带统计,排序功能



# 缓存性能

* 缓存命中率

![image-20200928115207215](https://gitee.com/hss012489/picbed/raw/master/picgo/1601265132921-image-20200928115207215.png)

glide的aop点:

使用aspectJ对RequestListener的onResourceReady进行切面,统计请求总数,缓存命中次数

![image-20200928115534731](https://gitee.com/hss012489/picbed/raw/master/picgo/1601265334758-image-20200928115534731.png)

![image-20200928115646966](https://gitee.com/hss012489/picbed/raw/master/picgo/1601265407008-image-20200928115646966.png)