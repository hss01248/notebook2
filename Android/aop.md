# AOP

# ASM

## 一个asm实现的方法拦截器

https://github.com/zhuguohui/MehodInterceptor

# java assist

## 工具

https://github.com/didi/DroidAssist/blob/master/docs/wiki.md

> 在方法调用的地方进行修改,可以修改系统api的调用.但不能修改系统api内部实现.
>
> 需要把替换写到xml里

## 修改前后对比

```
<Replace>
    <MethodCall>
        <Source>
           int android.util.Log.d(java.lang.String,java.lang.String)
        </Source>
        <Target>
            $_=com.didichuxing.tools.test.LogUtils.log($1,$2);
        </Target>
    </MethodCall>
</Replace>
```

处理前的class：

```
public class MainActivity extends Activity {
    public static final String TAG = "MainActivity";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        Log.d(TAG, "MainActivity onCreate");
    }
}
```

处理后的 class：

```
public class MainActivity extends Activity {
    public static final String TAG = "MainActivity";

    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        String var2 = "MainActivity";
	    String var3 = "MainActivity onCreate";
        int var4 = LogUtils.log(var2, var3); // The target method using custom log method.
    }
}
```

# aspectJ

> 针对方法,修改的内容存在方法内部. 因此无法修改系统api, java,Android等系统sdk的api
>
> 优势: 直接在java代码里写实现,仅通过注解来连接切面

## 语法

https://www.cnblogs.com/gy19920604/p/6051249.html



## 生成的代码内容:

![image-20201127172113969](https://gitee.com/hss012489/picbed/raw/master/picgo/1606468879188-image-20201127172113969.jpg)

## 工具

https://github.com/HujiangTechnology/gradle_plugin_android_aspectjx

### 插件使用指导

环境: 

Android studio4   

com.android.tools.build:gradle:4.1.0"

gradle-6.5

在项目根目录的build.gradle

```groovy
buildscript {
  ...
    dependencies {
      ...
      classpath "com.android.tools.build:gradle:4.1.0"
        classpath 'com.hujiang.aspectjx:gradle-android-plugin-aspectjx:2.0.10'
    }
}
```

 写一个单独的gradle文件,apply到主项目的build.gradle:

比如文件名为aspectjconfig.gradle

那么使用apply from: "aspectjconfig.gradle"

该脚本内容为:

```groovy
project.dependencies{
    implementation 'org.aspectj:aspectjrt:1.9.5'
   api 'com.github.hss01248.aop-android:logforaop:1.0.1'
}
def isNoop(){
    for (String s : gradle.startParameter.taskNames) {
        if (s.contains("ultiChannel") | s.contains("elease")) {
            return true
        } else if (s.contains("ebug") | s.contains("ommon")) {
            return false
        }
    }
    return null
}
if(!isNoop()){
    apply plugin: 'com.hujiang.android-aspectjx'
//为加快编译速度,需要自己将扫描的包路径添加到include里.  性能差距: 4min vs 4s
    aspectjx {
//排除所有package路径中包含`android.support`的class文件及库（jar文件）:Invalid byte tag in constant pool
        exclude 'com.google','com.taobao','com.alibaba','module-info','com.squareup.haha','versions.9','com.tencent',,'android.support',
                'androidx',
                'com.squareup',
                'com.alipay',
                'org.apache',
                'com.alipay',
                'com.facebook',
                'cn.jiguang',
                'com.github',
                'com.meizu',
                'com.huawei',
                'com.qiyukf',
                'com.sina',
                'io.reactivex',
                'de.greenrobot.event',
                'com.netease.neliveplayer',
                'com.umeng',
                'im.yixin',
                'com.commonsware',
                'io.fabric',
                'rx.android',
                'com.android'
        //必须要加入aspect注解所在的包路径
        include 'xxx'
      
    }
}
```



### 专门给aspectj用的log工具

```groovy
api 'com.github.hss01248.aop-android:logforaop:1.0.1'
```

> 给切面打印类名,对象hash,方法名,方法参数值,返回值,耗时

## 出现编译文件的解决办法

* 检查切面表达式是否正确,一般出现zip file is empty, not found,可能是表达式错误
* exclude掉报错里出现的包路径,类



# 其他骚操作

## 替换第三方库的某个类

https://github.com/skyNet2017/JarFilterPlugin

##  对系统/语言 sdk内部进行hook

epic等