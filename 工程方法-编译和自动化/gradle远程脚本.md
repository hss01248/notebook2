# gradle远程脚本

#  1 本地脚本插件使用

格式

```groovy
apply from: 'sharpImage.gradle'//和脚本同一个目录
apply from: "${rootProject.rootDir}/bugly.gradle"//根目录下
```

可以在项目根目录的build.gradle里,也可以放到子module的build.gradle里

一般通过这种方式将不同功能的脚本拆分到不同的gradle文件中,便于管理.



# 2 远程脚本

> 将一些通用的gradle放到远程,以进一步提高复用性.避免了文件拷来拷去.

其对gradle脚本里各对象的访问与本地脚本插件完全一致.

## 放在gitlab上

> 通过gitlab的api v4来访问

示例:

```groovy
apply from:  'https://git.私有化部署的gitlab域名.com/api/v4/projects/projectId(工程id)/repository/files/目录名%2F文件名/raw?ref=分支名&private_token=token内容'
//子路径里的/要写成%2F
```

## 放到github上

> 通过rawapi或者jsdeliver(cdn)来访问

```groovy
apply from: 'https://raw.githubusercontent.com/hss01248/flipperUtil/master/remote_forceokhttp3_12.gradle'

中国国内可使用cdn:
  apply from: 'https://cdn.jsdelivr.net/gh/hss01248/flipperUtil@master/remote_forceokhttp3_12.gradle'
  
//格式:  https://cdn.jsdelivr.net/gh/github用户名/仓库名@分支名/目录/文件名
```



# 3 一些脚本示例

## 使用bugly

bugly.gradle放到项目根目录下.

在bugly.gradle里:

```groovy
apply plugin: 'bugly'

bugly {
    appId = 'App ID' // 注册时分配的App ID
    appKey = 'App Key' // 注册时分配的App Key
}
```



在app的build.gradle里:

```groovy
apply from: "${rootProject.rootDir}/bugly.gradle"//根目录下
```



## 4 如何一个url解决带gradle插件的库的依赖问题



### 一般带插件的库,用法都是类似这样(以doreamkit为例子):

在项目的 `build.gradle` 中添加 classpath。

```groovy
buildscript {
    dependencies {
        …
        classpath 'io.github.didi.dokit:dokitx-plugin:3.4.0-alpha02'
        …
    }
}
```

在 app 的 `build.gradle` 中添加 plugin。

```groovy
apply plugin: 'com.didi.dokit'

dependencies {
    …
    debugImplementation 'io.github.didi.dokit:dokitx:3.4.0-alpha02'
    releaseImplementation 'io.github.didi.dokit:dokitx-no-op:3.4.0-alpha02'
    …
}
```

**插件配置选项:** 添加到app module 的build.gradle文件下 与android {}处于同一级

```groovy
dokitExt {
    //通用设置
    comm {
        //地图经纬度开关
        gpsSwitch true
        //网络开关
        networkSwitch true
      .....}
}
```



## 全部写到一个脚本里,如下:

> 主要是apply plugin的时机要在ProjectEvaluationListener的afterEvaluate里,相当于添加在app.gradle的最末尾

```groovy
import java.util.function.Consumer

rootProject.buildscript {
    repositories {
        jcenter()
        mavenCentral()
        mavenLocal()
        google()
    }
    dependencies {
        classpath 'io.github.didi.dokit:dokitx-plugin:3.4.0-alpha02'
    }
}

/*rootProject.allprojects{
    apply plugin: 'McImage'
}*/

rootProject.allprojects.forEach(new Consumer<Project>() {
    @Override
    void accept(Project project) {
        println("project:" + project);
        project.repositories {
            jcenter()
            mavenCentral()
            mavenLocal()
            google()
        }
        /*project.apply {
        //无效
            plugin: 'com.example.myplugin'
        }*/
    }
})

gradle.addProjectEvaluationListener(new ProjectEvaluationListener() {
    @Override
    void beforeEvaluate(Project project) {
        println("==--->beforeEvaluate:开始解析当前build.gradle: " + project);
    }

    @Override
    void afterEvaluate(Project project, ProjectState projectState) {
        println("==--->afterEvaluate: 解析当前module下build.gradle完成: " + project);

        if (project.plugins.findPlugin("com.android.application") != null) {
            project.apply plugin: 'com.didi.dokit'
            project.dependencies {
                debugImplementation 'io.github.didi.dokit:dokitx:3.4.0-alpha02'
                releaseImplementation 'io.github.didi.dokit:dokitx-no-op:3.4.0-alpha02'
            }
            project {
                dokitExt {
                    //通用设置
                    comm {
                        //地图经纬度开关
                        gpsSwitch true
                        //网络开关
                        networkSwitch true
                        //大图开关
                        bigImgSwitch true
                        //webView js 抓包
                        webViewSwitch true
                    }

                }
            }

            //一些全局依赖配置可以在这里处理
            project.configurations {
                all*.exclude group: 'com.google.guava', module: 'listenablefuture'
                all {
                    resolutionStrategy {
                        force "com.google.android.material:material:1.2.1"
                    }
                }
            }
        }
    }
})


subprojects {
    //println("project2:"+project);
    // //无效
    //project.apply plugin: 'com.example.myplugin'
}
```



## 引用方式:

> 根目录的build.gradle的buildscript中

```groovy
buildscript {
    apply from: 'dokit.gradle'
    repositories {
        google()
        jcenter()
    }
    dependencies {
        classpath "com.android.tools.build:gradle:4.1.0"
}
```

