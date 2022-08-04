# gradle task

# 1. 定义一个task,插入到一个已知task之前/之后,使运行改已知task时,自动运行你定义的task

示例: 将一个打印版本号和路径的task挂载到uploadArchives之后:

```groovy
group='com.hss01248.plugin2'
version='1.0.03'

uploadArchives {
    repositories {
        mavenDeployer {
            //提交到远程服务器：
            // repository(url: "http://www.xxx.com/repos") {
            //    authentication(userName: "admin", password: "admin")
            // }
            //本地的Maven地址设置为D:/repos
            repository(url: mavenLocal().getUrl())
        /*    pom.artifactId = '项目信息'
            pom.version = '版本信息'
            repository(url: '私服仓库地址') {
                authentication(userName: '账号', password: '密码')
            }
            snapshotRepository(url: '私服快照地址') {
                authentication(userName: '账号', password: '密码')
            }*/
        }
    }
}

task task1(){
    doLast{
        def url = uploadArchives.repositories.mavenDeployer.repository.url
        def groupId = uploadArchives.repositories.mavenDeployer.pom.groupId
        def artifactId = uploadArchives.repositories.mavenDeployer.pom.artifactId
        def version = uploadArchives.repositories.mavenDeployer.pom.version
        println "发布版本: $groupId:$artifactId:$version"
        println "url: $url"
    }
}
```

挂载的操作:

```groovy
//https://juejin.cn/post/6844904016778903560
//https://juejin.cn/post/6982379643311489032

afterEvaluate {
    // 1. 找到需要依赖自己 Task的构建流程的Task
    def uploadArchives = tasks.findByName("uploadArchives")
    def task1 = tasks.findByName("task1")
    println "uploadArchives=$uploadArchives"
    // 2. 通过finalizedBy 方法，挂载到指定Task之后
    if(uploadArchives != null){
        uploadArchives.finalizedBy(task1)
    }
  
    //示例2: 插入到uploadArchives之前:
    def task2 = tasks.findByName("task2")
    // 2. 通过dependsOn 方法，插入到指定Task之前
    if(uploadArchives != null){
        uploadArchives.dependsOn(task2)
    }
  
}
```

运行uploadArchives,然后可以看到日志:

![image-20220804114918258](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac2/1659584958414-image-20220804114918258.jpg)



# 2 .将一个task插入到本来已有依赖关系的两个task之间:

>  mustRunAfter 结合 dependsOn
>
> Gradle 的mustRunAfter是个大坑，必须要配合dependsOn来用。。。

![image-20210628172923093](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac2/1659585110847-8490c81992db468fb27a05f3b44ad952~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

```groovy
afterEvaluate {

  // 1. 找到需要挂接的Task
    def mergeResourcesTask = tasks.findByName("mergeDebugResources")
    def processDebugResourcesTask = tasks.findByName("processDebugResources")

  // 2.让自定义的 Task 在 mergeDebugResources 任务之后且在 processDebugResources 之前执行
    checkBigImage.mustRunAfter(mergeResourcesTask)
    processDebugResourcesTask.dependsOn(checkBigImage)
}

作者：LittlePlum
链接：https://juejin.cn/post/6982379643311489032
来源：稀土掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



# 3 打印task依赖树

```groovy
apply from:"https://raw.githubusercontent.com/hss01248/flipperUtil/dev/z_config/deps/depsLastestChecker.gradle"

tasks面板里的deps/listXxxTask
```



# 参考资料

[从Gradle生命周期到自定义Task挂接到Build构建流程全解](https://juejin.cn/post/6982379643311489032)