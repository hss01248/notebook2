# gradle依赖管理



# 锁定依赖版本



# 排除依赖

# 中断依赖传递链





# 强制刷新依赖

snapshot依赖默认本地缓存24h,所以发布新的sanpshot时,一般都需要强刷.

参考:[Gradle强制刷新单模块依赖](https://blog.csdn.net/zhe_d/article/details/114686679)

有几种方式:

## 命令行方式:  

>  会强制忽略所有模块缓存.不仅仅包括snapshot,还包括正式版本
>
> 作用于当次. 此次构建速度会减慢. 

```cmake
Windows:    
gradlew build --refresh-dependencies 


Mac:
./gradlew build --refresh-dependencies  
```

## gradle脚本方式: 

```groovy
configurations.all {
    resolutionStrategy.cacheDynamicVersionsFor 10, 'minutes'
    // resolutionStrategy.cacheChangingModulesFor 4, 'hours'
    resolutionStrategy.cacheChangingModulesFor 0, 'seconds'
}

```

## 两个概念:

DynamicVersions是指:

```groovy
implementation 'com.example:library:1.+'
```

ChangingModules是指snapshot:

```groovy
implementation 'org.example:library:1.0.0-SNAPSHOT'
```



对于nexus来说,一般的管理方式是:

 snapshot可以一直发

![image-20210425150827718](https://gitee.com/hss012489/picbed/raw/master/picgo/1619334514427-image-20210425150827718.jpg)

而release也可以一直发同一个版本,后发的会覆盖前一个发的.

一般推荐关闭正式版本的覆盖功能,不允许覆盖,只允许升级.

如果非要覆盖,那么gradle此处可以相应配置:

即声明对应的正式版本也是可变化的,那么就可以同snapshot一样刷新

```groovy
implementation('org.example:library:1.0.0'){
        changing = true
    }
```





