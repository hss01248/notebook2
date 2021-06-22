# 获取本app的下载/安装来源

# apk包从哪里下载的?

只能通过预置渠道名到apk文件内部,安装后打开app时读取对应的字节而实现.

比如渠道名的位置 内嵌huawei字符串的包发到华为市场,内嵌xiaomi字符串的发到小米市场.

但是

如果这个apk被其他市场爬取,就识别错误了. 比如小米渠道的包被xx市场爬到,那么安装后打开,还是被识别成小米市场的渠道包.



# apk被谁安装的?

```java
packageManager.getInstallerPackageName(DeviceReporter.app.getPackageName());

或者使用: 

try {
   installSourceInfo =  packageManager.getInstallSourceInfo(DeviceReporter.app.getPackageName());
    //packageManager.getPackageInstaller().
} catch (PackageManager.NameNotFoundException e) {
    e.printStackTrace();
}
```

其中,installSourceInfo包含两方面信息:

安装是被谁发起的

安装是被哪个apk安装器执行的

```java
public final class InstallSourceInfo implements Parcelable {

  
  //安装是被谁发起的: The name of the package that requested the installation, or null if not available.
    @Nullable private final String mInitiatingPackageName;

    @Nullable private final SigningInfo mInitiatingPackageSigningInfo;
/**
     * The name of the package on behalf of which the initiating package requested the installation,
     * or null if not available.
     * <p>
     * For example if a downloaded APK is installed via the Package Installer this could be the
     * app that performed the download. This value is provided by the initiating package and not
     * verified by the framework.
     * <p>
     * Note that the {@code InstallSourceInfo} returned by
     * {@link PackageManager#getInstallSourceInfo(String)} will not have this information
     * available unless the calling application holds the INSTALL_PACKAGES permission.
     */
    @Nullable private final String mOriginatingPackageName;

    @Nullable private final String mInstallingPackageName;//安装是被哪个apk安装器执行的
```



> 用intent发起一次apk安装时,如果不指定安装器的包名,那么会弹出系统所有的安装器供用户选择
>
> 如果指定安装器的包名,那么就直接调用指定安装器执行安装.
>
> 一般对于应用市场来说,肯定是指定自己来执行安装.

```java
Intent install = new Intent(Intent.ACTION_VIEW);
install.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
ComponentName cn = new ComponentName("com.example.jay.test",     
"com.example.jay.test.MainActivity");     
install.setComponent(cn);     
File apkFile = new File(Environment.getExternalStorageDirectory() + "/download/" + "app.apk";
install.setDataAndType(Uri.fromFile(apkFile)), "application/vnd.android.package-archive");
startActivity(install)
  //注意7.0 uri expose兼容,8.0 apk安装权限申请
```



> 一般app都发布到指定的几个市场,我们知道这几个市场的包名. 一个app安装后,它的安装信息应该也是在这几个市场包名内.
>
> 如果是被其他xx市场爬取,那么用户在那个xx市场下载,安装,
>
> 那么一般来说, apk的安装发起,安装执行的信息,都是那个xx市场.