# 如何将react native打包成一个aar

# rn打包bundle

```shell
npx react-native bundle --platform android --dev false --entry-file index.js --bundle-output android/com/your-company-name/app-package-name/src/main/assets/index.android.bundle --assets-dest android/com/your-company-name/app-package-name/src/main/res/
或
npx react-native bundle --platform android --dev false --entry-file index.js --bundle-output index.android.bundle --assets-dest android/
```





> 一句话总结: 本地文件夹方式的maven依赖改成远程maven仓库依赖,同时rn的打包产物bundle文件放到子module的assets目录下. 


# 首先 ,如何在现有Android项目中引入 react native:

https://reactnative.cn/docs/integration-with-existing-apps

开发模式为在本地../node_modules/里寻找react-native依赖和webkit的依赖

![image-20200522141821916](http://qa78tmc6l.bkt.clouddn.com/uPic/2020-05-22-14-18-29-image-20200522141821916.png)

![image-20200522142101859](http://qa78tmc6l.bkt.clouddn.com/uPic/2020-05-22-14-21-03-image-20200522142101859.png)

![image-20200522142239812](http://qa78tmc6l.bkt.clouddn.com/uPic/2020-05-22-14-22-42-image-20200522142239812.png)

其实就是在本地整了这两个maven仓库而已

![image-20200522143520961](http://qa78tmc6l.bkt.clouddn.com/uPic/2020-05-22-14-35-23-image-20200522143520961.png)

如此依赖,项目要编译成功,需要始终有本地的文件夹,主要是aar和porm文件,

是否可以将本地文件夹中的react-native依赖和webkit的依赖放到远程呢?当然是可以的

把相关的依赖发布到远程就可以了,私服nexus,或者maven center

目前,react-native已经有人发布到了maven center: 直接拿来用就行了

https://mvnrepository.com/artifact/com.walmartlabs.ern/react-native

webkit/chrome没有人发布. 好像是npm管理的. maven拉不到.

webkit/chrome 内核的pom文件里没有依赖其他的,也可以直接aar放到libs中.

,但比较麻烦,还是放到远程(nexus)比较方便

https://www.cnblogs.com/aimqqroad-13/p/8514274.html

![image-20200522144235753](http://qa78tmc6l.bkt.clouddn.com/uPic/2020-05-22-14-42-37-image-20200522144235753.png)



# 那么,最终形态是:

![image-20200527200612573](http://hss01248.tech/uPic/2020-05-27-20-06-14-image-20200527200612573.png)



主工程里:

![image-20200522144315828](http://qa78tmc6l.bkt.clouddn.com/uPic/2020-05-22-14-43-17-image-20200522144315828.png)

## 如何上传:

nmp管理变成maven管理:



用管理员账户登录nexus,手动上传aar和porm

### 或者用命令上传:mvn deploy

https://blog.csdn.net/chenaini119/article/details/52764543

http://maven.apache.org/plugins/maven-deploy-plugin/usage.html



## 

https://www.jianshu.com/p/2ef1642b769b

#### 第一步：在settings.xml中配置私服用户信息，要与上文的id相符合



```xml
  <servers>
      <server>
        <id>releases</id>
        <username>android-jinchuang</username>
        <password>jinchuang</password>
      </server>
      <server>
        <id>snapshots</id>
        <username>android-jinchuang</username>
        <password>jinchuang</password>
      </server>
  </servers>
```



## 然后命令行上传:

mvn deploy:deploy-file -DgroupId=org.webkit -DartifactId=android-jsc -Dversion=r245459 -Dfile=android-jsc-r245459.aar -Durl=https://git.xxxx.com:8856/repository/maven-releases/ -X -DrepositoryId=releases -DpomFile=android-jsc-r245459.pom

### 参数说明:

mvn deploy:deploy-file

-DgroupId=com.alibaba.csp  //groupId

-DartifactId=sentinel-annotation-aspectj // artifactId

-Dversion=1.2-SNAPSHOT  //version

-Dfile=g:\sentinel-annotation-aspectj-1.4.0.jar  //jar包路径及jar包文件名

-Dpackageing=jar  //上传文件的格式

 -DpomFile=pom.xml  //重点在这， 必须单独上传jar中的pom.xml

-DrepositoryId=user-thirdparty //连接maven私服的登录名及密码， 在maven > setting.xml 中配置好的

-Durl=http://IP:8081/nexus/content/repositories/snapshots/ //上传的私服路径及目录

### 一个参数都不能少

如果少了 -DrepositoryId,则无法读取setting.xml里相应id的用户名密码,会401

如果少了-DpomFile,则会自动使用空的pom,少了原来pom中相关依赖

## 上传后:

![企业微信截图_79326649-d0ae-4f8c-a4e3-6bb6117a8c64](http://hss01248.tech/uPic/2020-05-27-19-30-21-企业微信截图_79326649-d0ae-4f8c-a4e3-6bb6117a8c64.png)



作者：WarrriorKing
链接：https://www.jianshu.com/p/2ef1642b769b
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。







## 如果使用code push,还需要在app.gradle中增加;

```
gradle.projectsEvaluated {
    android.buildTypes.each {
        // to prevent incorrect long value restoration from strings.xml we need to wrap it with double quotes
        // https://github.com/Microsoft/cordova-plugin-code-push/issues/264
        it.resValue 'string', "CODE_PUSH_APK_BUILD_TIME",
                String.format("\"%d\"", System.currentTimeMillis())
    }
}
```

## 疑问: assets能够打进aar中吗?

可以的:

![image-20200528103924470](http://hss01248.tech/uPic/2020-05-28-10-39-27-image-20200528103924470.png)





# 启动调试

https://reactnative.cn/docs/integration-with-existing-apps

## 1 端口映射 

adb reverse tcp:8081 tcp:8081

## 2 运行 Packager

运行应用首先需要启动开发服务器（Packager）。你只需在项目根目录中执行以下命令即可：

```
$ yarn start
```

## 3 使用chrome tools看js日志

 http://localhost:8081/debugger-ui

https://reactnative.cn/docs/debugging

## 4 或者使用单独的debug工具:

npm install -g react-devtools

安装完成后在命令行中执行`react-devtools`即可启动此工具：

```
react-devtools
```



# 发布到binary:

https://juejin.im/post/5858cc37570c35006916b718



各种包管理

![image-20200616104526150](/Users/hss/Library/Application Support/typora-user-images/image-20200616104526150.png)



