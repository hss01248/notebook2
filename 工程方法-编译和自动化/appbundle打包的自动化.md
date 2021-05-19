# appbundle打包的自动化

app bundle是谷歌的一个打包方式,输出的是未签名的aab文件

拿到aab文件不能直接安装,要先用谷歌提供的bundletools.jar基于aab生成apks文件,然后使用bundletools.jar install来安装apks文件

每次手动运行bundletools.jar命令行很蛋疼,可以结合shell脚本和gradle脚本,直接在运行bundleDebug或者bundleRelease打包时直接拿到aab,apks,以及解压apks得到真正的apk. 以及脚本运行apks



# 首先,在项目根目录下放置好bundletool和keystore

![image-20210519171454060](https://gitee.com/hss012489/picbed/raw/master/picgo/1621415701249-image-20210519171454060.jpg)

## bundlerelease打包输出路径如下:

使用outputs下的bundleapks作为生成的apks目录

![image-20210519171646907](https://gitee.com/hss012489/picbed/raw/master/picgo/1621415806940-image-20210519171646907.jpg)

# 然后编写shell脚本. 

本质上就是调用bundletools的命令:

其他无非是处理各种路径,删除上次的文件等问题. 

```shell
java -jar $bundletool build-apks --bundle=$aabFile --output=$apks --ks=$RELEASE_STORE_FILE --ks-pass=pass:$RELEASE_KEY_PASSWORD --ks-key-alias=$RELEASE_KEY_ALIAS --key-pass=pass:$RELEASE_KEY_PASSWORD
```

以及安装:

```shell
# 安装apks
java -jar $bundletool install-apks --apks=$apks
#打开app
adb shell am start -n 包名/activity名
```

> 此处将apks的生成和安装分离,均放置于项目根目录下

![image-20210519172132631](https://gitee.com/hss012489/picbed/raw/master/picgo/1621416092663-image-20210519172132631.jpg)

全部脚本如下:

## bundletool.sh

```shell
#!/bin/bash
set -e

#echo "\033[33m === 请输入aab文件名 === \033[0m"

#echo "\033[05m"

#read aabFileName
#bundletool解析出的apks路径
###获取脚本执行的目录
SCRIPTDIR="$( cd "$( dirname "$0"  )" && pwd  )"
echo "当前文件夹:"$SCRIPTDIR


#bundletool路径
bundletool=$SCRIPTDIR/doc/bundletools/bundletool-all-1.2.0.jar

#数字签名证书密码别名
RELEASE_KEY_PASSWORD=xxxxx
RELEASE_KEY_ALIAS=xxxxx
RELEASE_STORE_PASSWORD=xxxxx
#数字签名证书的文件路径
RELEASE_STORE_FILE=$SCRIPTDIR/doc/keystore/xxxx


#if [ -e $apks ]
#then
#    rm -rf $apks
#fi

#接收用户输入的aab文件路径
aabDir=$SCRIPTDIR/app/build/outputs/bundle/release/

echo "aabDir:"$aabDir

# 遍历文件夹outputs/bundle/release
files=$(ls $aabDir)
for filename in $files
do
echo $filename

aabFile=$aabDir$filename
apks=$SCRIPTDIR/app/build/outputs/bundleapks/$filename.apks
echo "aab文件路径:"$aabFile
echo "开始生成apks:"$apks
#先查找本地是否存在apks文件,存在就删除
if [ -e $apks ]
then
    rm -rf $apks
fi

java -jar $bundletool build-apks --bundle=$aabFile --output=$apks --ks=$RELEASE_STORE_FILE --ks-pass=pass:$RELEASE_KEY_PASSWORD --ks-key-alias=$RELEASE_KEY_ALIAS --key-pass=pass:$RELEASE_KEY_PASSWORD
# java -jar $bundletool install-apks --apks=$apks
echo "\033[32m === apks已经生成,开始自动zip解压(gradle脚本处),提取内部apk,也可以另外运行脚本(sh bun_install.sh)安装 === \033[0m"
echo "apks路径:点击打开:"$SCRIPTDIR/app/build/outputs/bundleapks/
done
```



## bun_install.sh(一键安装apks)

```shell
#!/bin/bash
set -e

#echo "\033[33m === 请输入aab文件名 === \033[0m"

#echo "\033[05m"

#read aabFileName
#bundletool解析出的apks路径
###获取脚本执行的目录
SCRIPTDIR="$( cd "$( dirname "$0"  )" && pwd  )"
echo "当前文件夹:"$SCRIPTDIR


#bundletool路径
bundletool=$SCRIPTDIR/doc/bundletools/bundletool-all-1.2.0.jar



#接收用户输入的aab文件路径
aabDir=$SCRIPTDIR/app/build/outputs/bundle/release/

echo "aabDir:"$aabDir

files=$(ls $aabDir)
for filename in $files
do
echo $filename

aabFile=$aabDir$filename
apks=$SCRIPTDIR/app/build/outputs/bundleapks/$filename.apks
echo "aab文件路径:"$aabFile
echo "apks:"$apks




java -jar $bundletool install-apks --apks=$apks
#打开app
adb shell am start -n 包名/activity全限定名

echo "\033[32m === 已安装aab到手机,请前往手机验证功能 === \033[0m"
done
```



> 以上脚本可以先使用命令行运行调通,再与gradle对接. 

# 使用gralde来运行上述shell脚本

> gradle打包监听的完成回调里,执行上述shell

在根目录的build.gradle里:

```groovy
this.gradle.addBuildListener(new BuildListener() {
    @Override
    void buildStarted(Gradle gradle) {
        println '--->buildStarted...'
        println(gradle.startParameter.taskNames)
    }

    @Override
    void settingsEvaluated(Settings settings) {
        println '--->settingsEvaluated...'
    }

    @Override
    void projectsLoaded(Gradle gradle) {
        println '--->projectsLoaded...'
    }

    @Override
    void projectsEvaluated(Gradle gradle) {
        println '--->projectsEvaluated...开始打包'
        println '--->根目录:'+ getRootDir().absolutePath
        

    }

    @Override
    void buildFinished(BuildResult result) {
        println '--->buildFinished...'
        println("taskNames:"+gradle.startParameter.taskNames)
        List<String> names = gradle.startParameter.taskNames;
       if(names != null && names.size()>0){
           if("bundleRelease".equalsIgnoreCase(names.get(0))){
             //只在bundleRelease打包时才执行脚本
               exec {
                   executable "./bundletool.sh"
               }
               println '--->bundletool finished'
               unzipApks();
           }
       }


    }
})
```

unzipApks()方法:

> groovy虽然提示不够只能,但是可以和java代码混着写,就是这么任性

```groovy
def unzipApks(){
    String dirPath = getRootDir().absolutePath+"/app/build/outputs/bundleapks/"
    println '--->目录:'+ dirPath
    File dir = new File(dirPath);
    if(!dir.exists()){
        println '--->目录不存在:'
        return
    }
    def files = dir.listFiles(new FilenameFilter() {
        @Override
        boolean accept(File file, String s) {
            return s.endsWith(".apks")
        }
    })
    if(files == null || files.length ==0){
        println '--->文件夹里没有apks文件'
        return
    }
    for (int i = 0; i < files.length; i++) {
        File file = files[i];
        File dir2 = new File(dir,file.getName());
        dir2.mkdirs();
        unZipFiles(file);

    }
}



def unZipFiles(File zipFile)  {

    try {
        ZipFile zip = new ZipFile(zipFile, Charset.forName("GBK"));//解决中文文件夹乱码
        String name = zip.getName().substring(zip.getName().lastIndexOf('\\')+1, zip.getName().lastIndexOf('.'));
        println("name:"+name)

        File pathFile = new File(name);
        if (!pathFile.exists()) {
            pathFile.mkdirs();
        }
        println("pathFile:"+name)

        for (Enumeration<? extends ZipEntry> entries = zip.entries(); entries.hasMoreElements();) {
            ZipEntry entry = (ZipEntry) entries.nextElement();
            String zipEntryName = entry.getName();
            InputStream inputStream = zip.getInputStream(entry);
            String outPath = (name +"/"+ zipEntryName).replaceAll("\\*", "/");

            // 判断路径是否存在,不存在则创建文件路径
            File file = new File(outPath.substring(0, outPath.lastIndexOf('/')));
            if (!file.exists()) {
                file.mkdirs();
            }
            // 判断文件全路径是否为文件夹,如果是上面已经上传,不需要解压
            if (new File(outPath).isDirectory()) {
                continue;
            }
            // 输出文件路径信息
//			System.out.println(outPath);
            println '--->outPath:'+outPath
            FileOutputStream out = new FileOutputStream(outPath);
            byte[] buf1 = new byte[1024];
            int len;
            while ((len = inputStream.read(buf1)) > 0) {
                out.write(buf1, 0, len);
            }
            inputStream.close();
            out.close();
        }
        System.out.println("******************apks解压完毕********************");
    }catch(Throwable throwable){
        throwable.printStackTrace()
    }


}
```



我们可以看到最终效果:

Standalones里的就是可以直接分发的apk

![image-20210519173150314](https://gitee.com/hss012489/picbed/raw/master/picgo/1621416710378-image-20210519173150314.jpg)