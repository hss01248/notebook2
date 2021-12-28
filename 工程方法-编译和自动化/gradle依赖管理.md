# gradle依赖管理



# 锁定依赖版本

## 全局

```groovy
project.configurations {
    all {
        resolutionStrategy {
            force "com.google.android.material:material:1.2.1"
        }
    }
}
```



# 排除依赖

# 中断依赖传递链

## 单个





## 全局

```groovy
project.configurations {
    all*.exclude group: 'com.github.yalantis'
    all*.exclude group: 'com.google.guava', module: 'listenablefuture'
}
```



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
project.configurations.all {
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





# 查看项目所有依赖:

```shell
./gradlew :app:dependencies --configuration releaseRuntimeClasspath > old.txt
```

![image-20210524194717499](https://gitee.com/hss012489/picbed/raw/master/picgo/1621856837601-image-20210524194717499.jpg)

几个release版本之间依赖的差异,可以使用:

https://github.com/JakeWharton/dependency-tree-diff

配合gradle脚本,输出报告

### 输出依赖树

比如:

```shell
#!/bin/bash
set -e
echo "生成依赖树到文件dependencies.txt中,每次建release时,以及发版后merge到master之前运行此脚本一次,便于通过git追踪各版本依赖的变化"
./gradlew :app:dependencies --configuration releaseRuntimeClasspath > dependencies.txt
```

![image-20210524200502542](https://gitee.com/hss012489/picbed/raw/master/picgo/1621857902588-image-20210524200502542.jpg)

### 输出依赖列表

核心api

```groovy
runtimeConfiguration.resolvedConfiguration.lenientConfiguration.allModuleDependencies.forEach { dependency: ResolvedDependency ->
                dependency.moduleArtifacts.forEach { artifact: ResolvedArtifact ->
                    val newDep = MavenCoordinates(
                        dependency.moduleGroup, dependency.moduleName,
                        dependency.moduleVersion, artifact.type, artifact.classifier
                    )
                }
}
```

定义task,挂载到工程,输出到文件:

https://github.com/hss01248/flipperUtil/blob/master/deps/depsParser.gradle

可以用于:

* 检测snapshot版本
* 检测过旧的版本
* 检查实际依赖版本







# 脚本里动态增删依赖

https://juejin.cn/post/6971807367184777246

https://github.com/hss01248/MyDataStore/blob/script/test.gradle

# 一键发布工程中所有lib到nexus

思路

解析release依赖树

从叶子开始发布

发布后替换所有用到该lib的地方为远程依赖,然后再发布上一层

脚本挂载到gradle的listener上,与工程完全解耦,仅远程脚本交互



通用脚本使用如下:

https://github.com/hss01248/flipperUtil/blob/master/deps/%E4%B8%80%E9%94%AE%E5%8F%91%E5%B8%83%E8%84%9A%E6%9C%AC.md





# 参考

[Gradle依赖管理](https://benweizhu.gitbooks.io/gradle-best-practice/content/dependency-management.html)

[第五十章. 依赖管理](http://gradledoc.githang.com/2.0/userguide/dependency_management.html)

