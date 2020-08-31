# 发布artifact

# maven

## 目的地:本地

```
apply plugin: 'maven'

group = 'com.github.skyNet2017'
version = '1.0.6.12'

uploadArchives {
    repositories {
        mavenDeployer {
            //repository(url: "file://[your local maven path here]")
            repository(url: mavenLocal().getUrl())
        }
    }
}
//artifactId默认为module名字
```

结果:

![image-20200812114037182](http://hss01248.tech/uPic/2020-08-12-11-40-37-image-20200812114037182.png)

## 目的地: nexus

## 目的地:maven center

## 方式: gradle

## 方式: 命令行

# jcenter

[Android 打包.aar文件，上传到Bintray后发布到Jcenter](https://blog.csdn.net/zhudfly2013/article/details/81365269)