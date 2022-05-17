#  结构化文档一键转代码的实践-埋点-翻译-java api注释

> 开发过程中,产品/运营给过来的一些结构化文档,最终要转化为代码,比如埋点,翻译,都是重复劳动,可以解析并生成代码,然后复制粘贴

# 翻译文档

表格->解析为list->生成对应平台的翻译配置

比如Android的xml

后台的properties文件

都是可以一键生成. 

一般以模块名-英文前20位为key



# 埋点

> 以神策为例

翻译表格->

解析为list->

每行生成代码,放到表格最后一列->

复制粘贴到工程中

## 神策埋点上报的自测/校对

debug开启时,神策上报成功后会打印日志:   SA.AnalyticsMessages: valid message: [{xxxx}]

但是这个日志是全量的数据,很多带$的,以及值为空的字段,我们实际的埋点文档其实只关心少数几个,

那么可以利用flipper的数据库界面来展示,从此不用再看日志

> 利用aop来拿到上报成功的数据:

切入点的选择:

* 代理/替换网络请求: 神策底层为urlconnection,可以通过okttp-urlconnection库替换成okhttp,也可以使用aop切入

  com.sensorsdata.analytics.android.sdk.AnalyticsMessages.sendHttpRequest()方法,将其实现替换为okhttp实现

* 找到打印 SA.AnalyticsMessages: valid message: 的地方,为com.sensorsdata.analytics.android.sdk.SALog.i(java.lang.String, java.lang.String), 切入,拿到valid message后面的字符串,然后解析

显然后者更加便捷.

使用aspectjx插件, 切面代码为:

```java
     @Around("execution(* com.sensorsdata.analytics.android.sdk.SALog.i(java.lang.String, java.lang.String))")
    public Object saLog(ProceedingJoinPoint joinPoint) throws Throwable{
      ...
    }
```

解析字符串,匹配,入库,按发生时间排序

![image-20220517170723475](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac/1652778443510-image-20220517170723475.jpg)



# 通过java代码和文档注释来生成代码

> 先例: swagger等,通过注解来生成代码

java代码本身也是一种结构化数据,编译时解析为语法树

遍历树,就能搞事情

https://github.com/skyNet2017/javadoc-extractor/blob/master/src/main/java/me/tomassetti/javadocextractor/AllJavadocExtractor.java

曾经使用此工具解析jsbridge的java接口,生成js的测试代码



