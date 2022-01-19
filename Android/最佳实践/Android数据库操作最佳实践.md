# Android数据库操作最佳实践

> 推荐使用greendao, 完全orm操作, 也支持手写sql. 
>
> 数据库查看使用加强版的flipperUtil. 可查看sd卡上的任意数据库文件.

# GreenDao工具链

# 基本使用

工程根build.gradle里添加插件

```groovy
buildscript {
    repositories {
        google()
        jcenter()
        mavenCentral()
        maven { url 'https://jitpack.io' }
    }
    dependencies {
        classpath 'org.greenrobot:greendao-gradle-plugin:3.3.0'
    }
}
```

app或library里添加依赖,插件,配置:

示例:

```groovy
dependencies {
    api 'org.greenrobot:greendao:3.3.0'
    implementation "net.zetetic:android-database-sqlcipher:4.4.3"//数据库加密, 在Android5 vivo上会崩溃.其他问题未发现.
    api "androidx.sqlite:sqlite:2.0.1"
    api 'io.github.yuweiguocn:GreenDaoUpgradeHelper:v2.2.1'//数据库升级不清库
}
apply plugin: 'org.greenrobot.greendao'
greendao {
    schemaVersion 1 //数据库版本号
    daoPackage 'com.hss01248.accountcache.db'
// 设置DaoMaster、DaoSession、Dao 包名
    targetGenDir 'src/main/java'//设置DaoMaster、DaoSession、Dao目录,请注意，这里路径用/不要用.
    generateTests false //设置为true以自动生成单元测试。
    targetGenDirTests 'src/main/java' //应存储生成的单元测试的基本目录。默认为 src / androidTest / java。
}
```



## 一般的使用方式:

```java
public class MyDbUtil {

    /**
     * 初始化GreenDao,直接在Application中进行初始化操作
     */
    private static void initGreenDao(Application context) {
        DaoMaster.OpenHelper helper = new DaoMaster.DevOpenHelper(context, "xxx.db");
        Database db = helper.getWritableDb();
        DaoMaster daoMaster = new DaoMaster(db);
        daoSession = daoMaster.newSession();
    }


  private static DaoSession daoSession;
    //对外提供的api
  public static DaoSession getDaoSession() {
      if(daoSession ==null){
          initGreenDao(AccountCacher.app);
      }
      return daoSession;
  }
}
```

## 升级不清库

默认的DaoMaster.DevOpenHelper升级时会清空数据:

```java
public void onUpgrade(Database db, int oldVersion, int newVersion) {
    Log.i("greenDAO", "Upgrading schema from version " + oldVersion + " to " + newVersion + " by dropping all tables");
    dropAllTables(db, true);
    onCreate(db);
}
```

所以需要用不清库的操作: 构建一个自己的DaoMaster.OpenHelper:

```groovy
dependencies {
    api 'io.github.yuweiguocn:GreenDaoUpgradeHelper:v2.2.1'//数据库升级不清库
}
```

```java
public class MySQLiteUpgradeOpenHelper extends DaoMaster.OpenHelper {
    public MySQLiteUpgradeOpenHelper(Context context, String name) {
        super(context, name);
    }

    public MySQLiteUpgradeOpenHelper(Context context, String name, SQLiteDatabase.CursorFactory factory) {
        super(context, name, factory);
    }
    @Override
    public void onUpgrade(Database db, int oldVersion, int newVersion) {
        MigrationHelper.migrate(db, new MigrationHelper.ReCreateAllTableListener() {
            @Override
            public void onCreateAllTables(Database db, boolean ifNotExists) {
                DaoMaster.createAllTables(db, ifNotExists);
            }
            @Override
            public void onDropAllTables(Database db, boolean ifExists) {
                DaoMaster.dropAllTables(db, ifExists);
            }
        }, DebugAccountDao.class);
    }
}
```



# 把数据库保存到sd卡的其他目录下:

```java
public class MyDBContext extends ContextWrapper {

    public MyDBContext(Context base) {
        super(base);
    }

    /**
     * 获得数据库路径，如果不存在，则自动创建
     */
    @Override
    public File getDatabasePath(String name) {
        //判断是否存在sd卡
        boolean sdExist = android.os.Environment.MEDIA_MOUNTED.equals(android.os.Environment.getExternalStorageState());
        if(!sdExist){//如果不存在,
            Log.e("SD卡管理：", "SD卡不存在，请加载SD卡");
            return null;
        }
        else{//如果存在
            //获取sd卡路径
            String dbDir=android.os.Environment.getExternalStorageDirectory().getAbsolutePath();
            dbDir += "/.yuv/databases";//数据库所在目录
            String dbPath = dbDir+"/"+name;//数据库路径
            //判断目录是否存在，不存在则创建该目录
            File dirFile = new File(dbDir);
            if(!dirFile.exists()) {
                dirFile.mkdirs();
            }
            //数据库文件是否创建成功
            boolean isFileCreateSuccess = false;
            //判断文件是否存在，不存在则创建该文件
            File dbFile = new File(dbPath);
            if(!dbFile.exists()){
                try {
                    isFileCreateSuccess = dbFile.createNewFile();//创建文件
                } catch (Exception e) {
                    e.printStackTrace();
                }
            } else {
                isFileCreateSuccess = true;
            }
            //返回数据库文件对象
            if(isFileCreateSuccess) {
                return dbFile;
            } else {
                return null;
            }
        }
    }

    @Override
    public SQLiteDatabase openOrCreateDatabase(String name, int mode,
                                               SQLiteDatabase.CursorFactory factory) {
        SQLiteDatabase result = SQLiteDatabase.openOrCreateDatabase(getDatabasePath(name), null);
        return result;
    }

    @Override
    public SQLiteDatabase openOrCreateDatabase(String name, int mode, SQLiteDatabase.CursorFactory factory,
                                               DatabaseErrorHandler errorHandler) {
        SQLiteDatabase result = SQLiteDatabase.openOrCreateDatabase(getDatabasePath(name), null);
        return result;
    }

}
```



# 数据库加密

使用sqlspher:

```groovy
dependencies {
    implementation "net.zetetic:android-database-sqlcipher:4.4.3"//数据库加密
}
```

```java
  Database db = helper.getEncryptedWritableDb("密码");
```



# 综合以上三个功能,如下:

```java
public class MyDbUtil {

    /**
     * 初始化GreenDao,直接在Application中进行初始化操作
     */
    private static void initGreenDao(Application context) {
        //MyDBContext内部指定数据库存储路径
       Context context2 = new MyDBContext(context);
       //升级自动迁移数据的工具
        DaoMaster.OpenHelper helper = new MySQLiteUpgradeOpenHelper(context2, "xxx.db");
        //加密.但是sqlitesipher在6.0以下版本的vivo等机型上有c层崩溃问题
      Database db = helper.getEncryptedWritableDb("密码");
        DaoMaster daoMaster = new DaoMaster(db);
        daoSession = daoMaster.newSession();
    }
```



# 数据库查看:

应用内部数据库可使用glance(纯手机端)或flipper或Android studio查看

>  sd卡根目录数据可使用flipperUtil查看

https://github.com/guolindev/Glance

https://github.com/hss01248/flipperUtil



# 示例代码位置

https://github.com/hss01248/accountCacher/blob/master/accountcache/build.gradle