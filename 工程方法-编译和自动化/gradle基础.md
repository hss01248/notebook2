# gradle基础

# 基本对象

gradle

project



# 属性

## 如何获取gradle.properties文件里的属性:

是否有这个属性的判断:

无法使用gradle.hasProperty("flipper_use_aspectjx")和gradle.getProperties().contains来判断

只能用try-catch来判断:

```groovy

def doUseAspectjx(){
    try {
        return flipper_use_aspectjx
    }catch(Throwable throwable){
        throwable.printStackTrace()
        return false;
    }
}
```

```groovy
println("<--------flipper_use_aspectjx 直接取 :"+doUseAspectjx());
println("<--------flipper_use_aspectjx 从gradle.getProperties()里取:"+gradle.getProperties().get("flipper_use_aspectjx"));

//打印结果:
<--------flipper_use_aspectjx 直接取 :false
<--------flipper_use_aspectjx 从gradle.getProperties()里取:null
//定义了属性时的
<--------flipper_use_aspectjx 直接取 :true
<--------flipper_use_aspectjx 从gradle.getProperties()里取:null
```





# 非debug时也使用debug签名

```groovy
signingConfigs {
        debug {
            
        }
    }

    buildTypes {
        foo {
            debuggable true
            jniDebugBuild true
            signingConfig signingConfigs.debug
        }
    }
```

