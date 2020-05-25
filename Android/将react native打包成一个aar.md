# 如何将react native打包成一个aar

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

https://www.cnblogs.com/aimqqroad-13/p/8514274.html



# 那么,最终形态是:

![image-20200522144235753](http://qa78tmc6l.bkt.clouddn.com/uPic/2020-05-22-14-42-37-image-20200522144235753.png)



主工程里:

![image-20200522144315828](http://qa78tmc6l.bkt.clouddn.com/uPic/2020-05-22-14-43-17-image-20200522144315828.png)

用管理员账户登录nexus,手动上传aar和porm,或者用命令上传:

https://blog.csdn.net/chenaini119/article/details/52764543

mvn deploy:deploy-file -DgroupId=org.apache.thrift -DartifactId=libthrift -Dversion=1.12 -Dpackaging=jar -Dfile=D:\thrift-0.9.2.jar -Durl=http://ip:port/nexus/content/repositories/thirdparty/ -DrepositoryId=thirdparty 

http://maven.apache.org/plugins/maven-deploy-plugin/usage.html



## 命令上传

https://www.jianshu.com/p/2ef1642b769b

#### 第一步：在要上传的模块的pom.xml文件中添加如下代码



```xml
  <distributionManagement>
           <repository>
              <id>releases</id>
              <name>internal releases</name>
              <url>http://192.168.1.221:8081/repository/maven-releases</url>
          </repository>
          <snapshotRepository>
              <id>snapshots</id>
              <name>internal snapshot</name>
              <url>http://192.168.1.221:8081/repository/maven-snapshots</url>
          </snapshotRepository>
  </distributionManagement>
```

#### 第二步：在settings.xml中配置私服用户信息，要与上文的id相符合



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



作者：WarrriorKing
链接：https://www.jianshu.com/p/2ef1642b769b
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

















