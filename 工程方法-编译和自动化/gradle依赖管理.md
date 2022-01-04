# gradle依赖管理

# 历史演进

* 1 最原始的: 拷代码,自己编译. 目前c和c++的依赖管理就是这个水平,没有统一的包管理工具

* 2 帮你编译好,你去官网下载jar包,放到libs目录下

* 3 所有jar到放到一个统一的地方,按统一的规则来组织,上传,下载.

> 说jar包只是例子,aar,js,python库,都是这个范畴

衍生出两个核心概念

# 核心概念

## 仓库

用于存储,管理jar包等.

又有中央仓库,镜像仓库,私有仓库(私服),本地仓库之分. 也是多级缓存的体现

![image-20220102193901183](https://cdn.jsdelivr.net/gh/hss01248/picbed@master/pic/image-20220102193901183.png.jpg)

> 一个项目里会用到哪些仓库?

```
Google : https://dl.google.com/dl/android/maven2/
BintrayJCenter : https://jcenter.bintray.com/
MavenRepo : https://repo.maven.apache.org/maven2/
maven : https://git.xxxx.com:8888/repository/maven-snapshots/
maven2 : https://git.xxxx.com:8888/repository/maven-releases/
maven3 : https://git.xxxx.com:8888/repository/maven-public/
MavenLocal : file:/Users/hss/.m2/repository/
maven4 : https://jitpack.io
Google2 : https://dl.google.com/dl/android/maven2/
BintrayJCenter2 : https://jcenter.bintray.com/
MavenRepo2 : https://repo.maven.apache.org/maven2/
maven5 : https://maven.fabric.io/public
maven6 : https://jitpack.io
Google3 : https://dl.google.com/dl/android/maven2/
BintrayJCenter3 : https://jcenter.bintray.com/
MavenRepo3 : https://repo.maven.apache.org/maven2/
maven7 : https://jitpack.io
MavenRepo4 : https://repo.maven.apache.org/maven2/
BintrayJCenter4 : https://jcenter.bintray.com/
Google4 : https://dl.google.com/dl/android/maven2/
flatDir : null
MavenLocal2 : file:/Users/hss/.m2/repository/
maven8 : https://maven.google.com
maven9 : https://jitpack.io
maven10 : https://maven.fabric.io/public
maven11 : https://oss.sonatype.org/content/repositories/snapshots/
maven12 : http://nexus2.tingyun.com/nexus/content/repositories/snapshots/
maven13 : https://git.xxxx.com:8888/repository/maven-snapshots/
maven14 : https://git.xxxx.com:8888/repository/maven-releases/
```

## 坐标

![image-20220102194837396](https://cdn.jsdelivr.net/gh/hss01248/picbed@master/pic/image-20220102194837396.png.jpg)

com.google.zxing:core:2.3.0

com.google.zxing--> groudId

core --> artifactId

2.3.0 -->version

URL 统一资源定位符

https://repo1.maven.org/maven2/com/google/zxing/core/2.3.0/



maven仓库搜索:

https://mvnrepository.com/





## 文件结构

### 远程仓库

> groupId/artifactId路径下,metadata.xml存放了版本列表信息

![image-20220102200436373](https://cdn.jsdelivr.net/gh/hss01248/picbed@master/pic/image-20220102200436373.png.jpg)

![image-20220102200524312](https://cdn.jsdelivr.net/gh/hss01248/picbed@master/pic/image-20220102200524312.png.jpg)

> 具体的版本号路径下,存储了这个版本发布的包
>
> 至少有pom.xml, 以及打包产物比如jar,aar,javadoc等

![image-20220102200759009](https://cdn.jsdelivr.net/gh/hss01248/picbed@master/pic/image-20220102200759009.png.jpg)

pom.xml

![image-20220102201409237](https://cdn.jsdelivr.net/gh/hss01248/picbed@master/pic/image-20220102201409237.png.jpg)



### 私服-nexus

开源,自行搭建.

### 任意公开目录

> 不想申请maven central账号,不想搭nexus, 不想用jitpack发,怎么搞? 
>
> 直接在任意公开目录,构建符合maven的文件结构,比如:

示例:

https://github.com/link-u/AndroidGlideAvifDecoder

```groovy
repositories {
    ...
    maven { url 'https://github.com/link-u/AndroidGlideAvifDecoder/raw/master/repository' }
    ...
}

...

implementation 'jp.co.link_u.library.glideavif:glideavif:0.8.1'
```



### 本地仓库

#### 本地maven仓库: mavenLocal()

或者增加其他文件夹:

```groovy
maven { url "${System.env.HOME}/creativesdk-repo/release" }
maven { url "../react-navive/android/gist" }
```



> 没有metadata.xml

![image-20220102201013895](https://cdn.jsdelivr.net/gh/hss01248/picbed@master/pic/image-20220102201013895.png.jpg)



![image-20220102201118920](https://cdn.jsdelivr.net/gh/hss01248/picbed@master/pic/image-20220102201118920.png.jpg)

#### gradle在本地的缓存文件夹

> 存放的是aar解压后的文件. 而不是像mavenLocal一样原原本本的jar包/aar包

![image-20220102202229073](https://cdn.jsdelivr.net/gh/hss01248/picbed@master/pic/image-20220102202229073.png.jpg)

![image-20220102202306086](https://cdn.jsdelivr.net/gh/hss01248/picbed@master/pic/image-20220102202306086.png.jpg)

> 文件名是那个依赖包的md5,无规律. 毕竟只是个缓存

![image-20220102202455855](https://cdn.jsdelivr.net/gh/hss01248/picbed@master/pic/image-20220102202455855.png.jpg)



纯jar包的话,就没有解压缩成文件夹

![image-20220102202742047](https://cdn.jsdelivr.net/gh/hss01248/picbed@master/pic/image-20220102202742047.png.jpg)

![image-20220102195125120](https://cdn.jsdelivr.net/gh/hss01248/picbed@master/pic/image-20220102195125120.png.jpg)



# 依赖版本

## 统一管理依赖版本

maven有dependencymanagement, 其子module不用指定版本号

gradle: 使用rootProject里ext定义额外属性或者使用[Version Catalog](https://juejin.cn/post/6997396071055900680)这个新特性

![image-20220102205243842](https://cdn.jsdelivr.net/gh/hss01248/picbed@master/pic/image-20220102205243842.png.jpg)

![image-20220102205256829](https://cdn.jsdelivr.net/gh/hss01248/picbed@master/pic/image-20220102205256829.png.jpg)

## 全局锁定依赖版本

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

## 单个

```groovy
api ('commons-httpclient:commons-httpclient:3.1'){
        exclude group:'commons-codec' //排除该group的依赖
        // exclude group:'commons-codec',module:'commons-codec'
        // group是必选项，module可选
    }
```

> 也可以替换传递的依赖:

```groovy
compile group:'a',name:'a',version:'l.0' {
    dependencies 'b:b:1.1'
}
```



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

一般推荐关闭正式版本的覆盖功能,**不允许覆盖,只允许升级**.

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



## 检查依赖列表里哪些版本有新版本,最新版本是多少,并输出报告

仓库+坐标,访问其maven-metadata.xml,解析,并与项目中的对比即可.

开启gradle的并发功能, 多线程并发请求加速, 主线程循环等待.

[depsLastestChecker.gradle](https://github.com/hss01248/flipperUtil/blob/master/deps/depsLastestChecker.gradle)

![image-20220102204000938](https://cdn.jsdelivr.net/gh/hss01248/picbed@master/pic/image-20220102204000938.png.jpg)



# 脚本里动态增删依赖

https://juejin.cn/post/6971807367184777246

https://github.com/hss01248/MyDataStore/blob/script/test.gradle

# 一键发布工程中所有lib到nexus

> maven本身就支持,而gradle需要自己写脚本实现

思路

解析release依赖树

从叶子开始发布(深度优先)

然后再发布上一层. 发布此层时发现为project依赖,则指定预先设置的groupId,artifactId,以及版本号再发布.

脚本挂载到gradle的listener上,与工程完全解耦,仅远程脚本交互.

![image-20220102204233456](https://cdn.jsdelivr.net/gh/hss01248/picbed@master/pic/image-20220102204233456.png.jpg)



![image-20220102204410685](https://cdn.jsdelivr.net/gh/hss01248/picbed@master/pic/image-20220102204410685.png.jpg)



通用脚本使用如下:

[Android代码库一键发布到nexus/本地maven](https://github.com/hss01248/flipperUtil/blob/master/deps/%E4%B8%80%E9%94%AE%E5%8F%91%E5%B8%83%E8%84%9A%E6%9C%AC.md)



# 参考

[Gradle依赖管理](https://benweizhu.gitbooks.io/gradle-best-practice/content/dependency-management.html)

[第五十章. 依赖管理](http://gradledoc.githang.com/2.0/userguide/dependency_management.html)







```groovy
project.afterEvaluate { 
    project.gradle.taskGraph.whenReady { 
        println("=======> print allTasks")
        println(project.gradle.taskGraph.allTasks) 
    } 
}

作者：弄码哥nomag
链接：https://juejin.cn/post/7043235150796161060
来源：稀土掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

