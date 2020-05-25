https://www.sohu.com/a/344302270_465908

https://github.com/No89757/Udid

https://sspai.com/post/58305

![img](https://cdn.sspai.com/2020/01/14/ad24e45b369edaeb6ccd1ef4183a1b2b.png)



![image-20200515145130757](http://qa78tmc6l.bkt.clouddn.com/uPic/2020-05-15-14-51-33-image-20200515145130757.png)



清数据后:



Android ID:

8.0之后卸载后会变

https://googledeveloperschina.blogspot.com/2017/04/android-o.html

IMEI:

需要权限,且10.0之后拿不到

# 如何解决卸载后再安装的变化问题:

## 利用谷歌备份服务(必须谷歌服务可用且与谷歌账号关联时才能用):

[Android 云同步之备份API](https://blog.csdn.net/twlkyao/article/details/8719151)

[使用 Android Backup Service 备份键值对](https://developer.android.com/guide/topics/data/keyvaluebackup.html?authuser=1)

无法对抗用户手动点击清除数据?

## 利用本地文件存储:

存到一个不容易删除的地方

其他辅助信息:googleAdid,adjustId

## 如何解决清数据后变化的问题:

```
/**
 * 可以对抗清除数据的操作,但无法对抗卸载后重装的操作
 * @param app
 * @return
 */
private static boolean isNotFirstInstall(Application app) {
    try {
        PackageInfo info =  app.getPackageManager().getPackageInfo(app.getPackageName(),0);
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            Log.d("cost","app uid:"+app.getApplicationInfo().uid+" storageuuid:"+app.getApplicationInfo().storageUuid.toString());
            //app uid:10114 storageuuid:41217664-9172-527a-b3d5-edabb50a7d69
            //app uid:10115 storageuuid:41217664-9172-527a-b3d5-edabb50a7d69
        }
        Log.d("cost","firstInstallTime:"+info.firstInstallTime+" lastUpdateTime:"+info.lastUpdateTime);
        //firstInstallTime:1589439159774 lastUpdateTime:1589444920079
        //卸载后重装: firstInstallTime:1589444980424 lastUpdateTime:1589444980424
        return info.firstInstallTime != info.lastUpdateTime;
    } catch (PackageManager.NameNotFoundException e) {
        e.printStackTrace();
        return false;
    }
}
```

## 如何判断是首次安装还是后续更新:

![企业微信截图_15894420348763](http://qa78tmc6l.bkt.clouddn.com/uPic/2020-05-14-15-43-23-企业微信截图_15894420348763.png)





# 总体方案设计:

# 参与的信息:

Android版本号: 数字,两位数

IMEI:无权限为空,Android版本号大于等于29为空. 如果能拿到数据,则不受卸载和恢复出厂设置影响. root后可更改

Android ID : Android版本号大于等于29时为空, 卸载后重装,8.0之上会变

自写uuid: 存到sp: 会被手动点击清除数据后清空

​                 存到外存某个隐藏文件夹: 需要存储权限,手机重置或用户删了文件后数据会清空

googleAdid: 需要谷歌服务可用. 用户可以点击谷歌设置中按钮重置

adjustId: adjust平台对设备唯一id的判定,需要和adjust服务器通信成功才能拿到不为空的值



靠谱程度:















```java
.put("deviceToken", FireBaseManager.getInstance().getFirebaseToken())
                .put("deviceId", )
                .put("imei", )
                .put("adjustId", )
                .put("googleAdid", )

```

https://www.jianshu.com/p/59440efa020c


官方文档:
https://developer.android.com/training/articles/user-data-ids
https://developer.android.com/reference/android/os/Build#getSerial()

| 属性   | Android ID | Build.serial | IMEI |
| ------ | ---------- | ------------ | ---- |
| 说明   | 8.0        | content3     |      |
| 示例   | 8.0        | content3     |      |
| 可变性 | content2   | content3     |      |
| 权限   | content2   | content3     |      |

## ANDROID_ID
在设备首次启动时，系统会随机生成一个64位的数字，并把这个数字以16进制字符串的形式保存下来，这个16进制的字符串就是ANDROID_ID，当设备被wipe后该值会被重置。
可以通过下面的方法获取：

 ```java
String ANDROID_ID = Settings.System.getString(context.getContentResolver(), Settings.System.ANDROID_ID);

 ```

ANDROID_ID可以作为设备标识，但需要注意：
1. 厂商定制系统的Bug：不同的设备可能会产生相同的ANDROID_ID：9774d56d682e549c。
2. 厂商定制系统的Bug：有些设备返回的值为null。
3. 设备差异：对于CDMA设备，ANDROID_ID和TelephonyManager.getDeviceId() 返回相同的值。

- 对于安装在8.0系统的应用来说，ANDROID_ID根据应用签名和用户的不同而不同。

ANDROID_ID的唯一决定于应用签名、用户和设备三者的组合。

两个规则导致的结果就是：

第一，如果用户安装APP设备是8.0以下，后来卸载了，升级到8.0之后又重装了应用，Android ID不一样；

第二，不同签名的APP，获取到的Android ID不一样。

## 序列号SerialNumber：

从Android 2.3开始，通过android.os.Build.SERIAL方法可获取到一个序列号。没有电话功能的设备也都需要上给出此唯一的序列号。

```java
public static String getSerial() {
        String serial = "";
        try {
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
                if (ActivityCompat.checkSelfPermission(Utils.getApp(), Manifest.permission.READ_PHONE_STATE)
                        == PackageManager.PERMISSION_GRANTED) {
                    serial = Build.getSerial();
                }
            } else {
                serial = Build.class.getField("SERIAL").get(null).toString();
            }
            return serial;
        } catch (Exception e) {
            return "";
        }
    }

```


缺点：

在某些设备上此方法会返回垃圾数据。
高版本需要权限

 


mobileInfo库里的算法:

https://github.com/guxiaonian/MobileInfo/blob/master/mobilehardware/src/main/java/com/mobile/mobilehardware/uniqueid/PhoneIdHelper.java

极光registerionid:
https://www.cnblogs.com/xd502djj/p/8137204.html

//设置系统配置文件中的数据，第一个参数固定的，但是需要上下文，第二个参数是保存的Key，第三个参数是保存的value
 boolean changeBluetoothName = Settings.System.putInt(getContentResolver(), "changeBluetoothName", 1);

//获取系统配置文件中的数据，第一个参数固定的，但是需要上下文，第二个参数是之前保存的Key，第三个参数表示如果没有这个key的情况的默认值
 int blueFlag = Settings.System.getInt(getContentResolver(), "changeBluetoothName", 0);

Android 平台
Android 上因为国内存在大量山寨设备的原因，正常的 IMEI, Mac Address, AndroidID 这些可以考虑用作唯一标识的值，都是不可以用的，因为这些值在一批设备中可能都是同一个值。

极光的基本思路是：

生成一个 DeviceID 保存到 Settings, External Storage。依赖本地存储，应用被卸载后重新安装这些存储里的 DeviceID 还在的话，就是同一个设备。这一条理论上解决 90% 的不变性问题。
DeviceID 之外增加补充规则：综合根据 IMEI, MAC Address, AndroidID 这几个值来判断，是否可能是老设备。
具体的逻辑细节，也是根据实际运行情况，以及收集到的反馈不断调整的，大多数逻辑可在服务器端调整。

iOS平台
鉴于 iOS 系统设计上限制设备唯一标识，所以极光一直使用 Device Token 作为标识，也因为极光推送本身就是需要 Device Token 这个值才可能运作的。

iOS 9 版本之后，每次卸载后重装都会导致 Device Token 变化，所以对于极光后台来说，都只能被识别为新用户。

极光 SDK 新版本增加了 IDFA 选项，在集成初始化 SDK 时可选把 IDFA 这个值设置进来，这样极光后台就优先根据 IDFA 值来识别用户，从有一定的可能性应用被卸载后重装还能识别回老设备。

IDFA 是广告标识符，是 iOS 专门为广告跟踪唯一地识别用户而设计的。在 iOS 设备上，设备 -> 隐私 -> 广告这个页面，有一个设置项：限制广告跟踪。默认是未选中状态的，即是关闭状态，是不限制的。用户可以选中，从而限制广告跟踪。设置项之外还有一个按钮：还原广告标识符…。如果用户点击了这个按钮，则 IDFA 值会变化。

https://blog.csdn.net/qq_22233425/article/details/104722516

https://github.com/happylishang/CacheEmulatorChecker

![image.png](http://47.106.241.169:8090/upload/2020/05/image-613c7259010149bb8c853f939619274d.png)