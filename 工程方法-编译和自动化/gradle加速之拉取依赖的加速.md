# gradle加速之拉取依赖的加速

# 前言

> 镜像配置都是常规操作,必要时也可以上代理.
>
> 自己搭的nexus本质也是一种镜像,可以代理maven中央仓库.



> 各个仓库的测速,可以使用这个脚本:
>
> 通过测速,调整仓库的顺序

```groovy
apply from: 'https://raw.githubusercontent.com/hss01248/flipperUtil/master/deps/depsLastestChecker.gradle'
```

![image-20220427204835585](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac/1651063715627-image-20220427204835585.jpg)

# 情况1 : 每次点击sync project with gradle files 都去拉取某个pom,且那个pom对应的版本真的不存在

![image-20220427194626339](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac/1651059991589-image-20220427194626339.jpg)

耗时:18s

![image-20220427194728442](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac/1651060048481-image-20220427194728442.jpg)

>  1 去对应gradle缓存里去看这个库在不在: 确实不在

![image-20220427194951868](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac/1651060191895-image-20220427194951868.jpg)

> 2 看com.github.CymChad:BaseRecyclerViewAdapterHelper:2.9.46-androidx这个到底在哪个仓库中. 直接先去maven中央仓库搜索:
>
> 发现TMD根本就没有这个版本的库. 

https://mvnrepository.com/artifact/com.github.CymChad/BaseRecyclerViewAdapterHelper?repo=mulesoft-public

![image-20220427195118740](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac/1651060278772-image-20220427195118740.jpg)

> 解决方案: 
>
> 方案1: 打印依赖树,看这个版本谁引入的,exclude掉
>
> 方案2: 直接强制指定这个库的版本为项目中实际用的版本,就不会去额外请求这个版本的pom. 如下:

```groovy
 all {
        resolutionStrategy {
            //gradle 刷新加速. 避免每次去刷新com.github.CymChad:BaseRecyclerViewAdapterHelper:2.9.46-androidx
            //2.9.46-androidx不存在,所以每次都会去拉取 ; 
            force 'com.github.CymChad:BaseRecyclerViewAdapterHelper:2.9.49-androidx'
```



# 情况2: 每次点击sync project with gradle files或者build,都去拉一堆的pom,且这些pom对应的版本在gradle cache里能找到

每次点击sync project with gradle files,都要**耗时3-5min**,下载一堆已经存在的库(gradle cache里已经有对应的版本)

这时早就配置好了下面的

```groovy
all{
  resolutionStrategy{
  // cache dynamic versions for 10 minutes
    cacheDynamicVersionsFor 24, 'hours'
    // don't cache changing modules at all
    cacheChangingModulesFor 24, 'hours'
  }
}
```



>  发现没有repository里没有配置mavenlocal, 配置一下就好了

类似这里提到的:

[Gradle 总是在每次 build 下载 pom 文件](https://mirai.mamoe.net/topic/380/gradle-%E6%80%BB%E6%98%AF%E5%9C%A8%E6%AF%8F%E6%AC%A1-build-%E4%B8%8B%E8%BD%BD-pom-%E6%96%87%E4%BB%B6?lang=zh-CN&page=1)

最后,刷新只要10s左右

