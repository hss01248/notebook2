# app技术选型

 

# 快速开发框架参考:

https://github.com/fly803/BaseProject

https://github.com/youth5201314/XFrame

https://github.com/tianshaojie/AndroidFine

https://github.com/limedroid/XDroidMvp

https://github.com/goldze/MVVMHabit

https://github.com/TommyLemon/Android-ZBLibrary

https://github.com/tianshaojie/AndroidFine

https://github.com/luojilab/DDComponentForAndroid

https://github.com/mqzhangw/JIMU : 组件化架子

https://github.com/xuexiangjys/XUI :基于QMUI_Android

https://github.com/HelloChenJinJun/NewFastFrame 1K star

https://github.com/xiaojinzi123/Component 组件化架子

 

 

 

# 各种技术选型

https://github.com/SenhLinsh/Android-Hot-Libraries

 

 

 

# 通用ui组件库

## Tencent/QMUI_Android

## QQ风格 

https://github.com/Tencent/QMUI_Android

https://qmuiteam.com/android/documents/

##  Genius-Android

小清新风格

![img](private/var/folders/2d/h_qly1js0lqb18sml00by7480000gn/T/com.kingsoft.wpsoffice.mac/wps-hss/ksohtml/wpsmJMQYI.jpg) 

 

https://github.com/qiujuer/Genius-Android

 

 

## material-components-android

material风格

 

https://github.com/material-components/material-components-android

 

 

## UIWidget

一个集成TabLayout、UIAlertDialog、UIActionSheetDialog、UIProgressDialog、TitleBarView(自带沉浸式标题栏)、CollapsingTitleBarLayout、RadiusView(圆角及状态背景设置View解放shape文件)、KeyboardHelper(软键盘控制及遮挡控制类)、StatusViewHelper(状态栏沉浸帮助类)、NavigationViewHelper（导航栏沉浸式帮助类）、AlphaViewHelper(View透明度控制帮助类) 等项目常用UI库

https://github.com/AriesHoo/UIWidget

 

# 一些框架集锦

https://github.com/Ericsongyl/AOSF

 

 

# 学习demo:

## Android Material Design 风格控件的学习及遇到的问题

 

https://github.com/CoderGuoy/Coder

 

 

 

 

 

架构图

 

 

 

 

 

 

# 架构组件

## 开发模式

### mvvm: 

livedata+ viewmodel+viewholder

### 组件化

Appjoin

https://juejin.im/post/5bb9c0d55188255c7566e1e2

https://github.com/PrototypeZ/AppJoint

 

## 生命周期:

### androidx.lifecycle

常规view中:感知声明周期,而不对外暴露方法,让类的功能高内聚

implements LifecycleObserver

 

public void setLifecycleOwner(@Nullable LifecycleOwner owner) {
  if (owner == null) {
    clearLifecycleObserver();
  } else {
    clearLifecycleObserver();
    mLifecycle = owner.getLifecycle();
    mLifecycle.addObserver(this);
  }
}

private void clearLifecycleObserver() {
  if (mLifecycle != null) {
    mLifecycle.removeObserver(this);
    mLifecycle = null;
  }
}

@OnLifecycleEvent(Lifecycle.Event.**ON_RESUME**)
public void open() {
  this.onPause();

}

 

 

### rxjava中:RxLifecycle

https://github.com/trello/RxLifecycle

以此构造无内存泄露的定时器,延时器等常用工具,

以及耗时任务(如网络请求).

## onActivityResult接收

https://github.com/VictorAlbertos/RxActivityResult

https://github.com/AnotherJack/AvoidOnResult

https://github.com/bbssyyuui/ActivityLauncher

 

### 与ARouter结合:

https://github.com/hss01248/arouter-api-onActivitResult

 

 

## 状态保存和恢复

https://github.com/PrototypeZ/SaveState

 

 

 

 

## activity栈管理

 

## app启动管理

https://github.com/NoEndToLF/AppStartFaster

 

 

 

 

 

 

 

 

## 网络

### HttpUtil

https://github.com/hss01248/HttpUtil

https://github.com/jeasonlzy/okhttp-OkGo

https://github.com/zhou-you/RxEasyHttp

https://github.com/yanzhenjie/NoHttp

https://github.com/liujingxing/okhttp-RxHttp

### 下载

https://github.com/lingochamp/FileDownloader

https://github.com/pengjianbo/FileDownloaderFinal

 

 

## 图片

### 图片加载

#### Imageloader

https://github.com/hss01248/ImageLoader

###  图片预览

### PhotoView

https://github.com/chrisbanes/PhotoView

### 大图区域解码

https://github.com/davemorrissey/subsampling-scale-image-view

#### BigImageViewer

基于subsampling-scale-image-view封装的带ui状态:

https://github.com/Piasy/BigImageViewer

#### Viewpager里查看大图

https://github.com/SherlockGougou/BigImageViewPager

### 长图

https://github.com/LuckyJayce/LargeImage

 

### 图片选择

#### YImagePicker

https://github.com/yangpeixing/YImagePicker

 

#### TakePhoto

https://github.com/crazycodeboy/TakePhoto 一条龙,但不支持androidx,依赖库版本比较低

#### EasyPhotos

兼容android 10，自定义相机拍照，相册选择(单选/多选)，文件夹图片选择(单选/多选)，视频选择，各界面根据状态栏颜色智能适配状态栏字体颜色变色为深色或浅色，根据使用场景智能适配沉浸式状态栏，内部处理运行时权限，支持Glide/Picasso/Imageloader等所有图片加载框架库的带默认勾选选中图片的能填充自定义广告的自定义Ui相机相册图片浏览选择器；更有拼图/文字贴纸/贴图/图片缩放/Bitmap图片添加水印/媒体文件更新到媒体库等众多Bitmap图片编辑功能的android Bitmap图片处理工具框架库。

 

https://github.com/HuanTanSheng/EasyPhotos

#### PictureSelector(推荐)

支持从相册获取图片、视频、音频&拍照，支持裁剪(单图or多图裁剪)、压缩、主题自定义配置等功能，支持动态获取权限&适配Android 5.0+系统的开源图片选择框架

 

https://github.com/LuckSiege/PictureSelector

 

https://github.com/zhihu/Matisse

 

 

### 图片裁剪

https://github.com/Yalantis/uCrop

 

### 截图保存

https://github.com/huazhiyuan2008/ViewToImage

https://github.com/SherlockGougou/DrawLongPictureDemo

## 数据库

可供选择: greendao,room,realm,ormlite

### Greendao

https://github.com/greenrobot/greenDAO

### Ormlite

## kv存储

 

https://github.com/No89757/LightKV

https://github.com/JeremyLiao/FastSharedPreferences

 

## 页面状态管理

### Loadsir

https://github.com/KingJA/LoadSir

 

### StatefulLayout

https://github.com/gturedi/StatefulLayout

 

## 初始化

### AppInit:

https://github.com/bingoogolapple/AppInit/blob/master/docs/user-manual.md

## 标题栏

通用，功能全面的自定义标题栏，支持沉浸式标题栏，颜色渐变，miui9

https://github.com/wuhenzhizao/android-titlebar

Android 仿 iOS UINavigationBar 风格的 TitleBar，适用于某些 UI 设计师只出 iOS 效果图的项目

https://github.com/bingoogolapple/BGATitleBar-Android

 

### 状态栏适配

https://github.com/gyf-dev/ImmersionBar

 

 

 

## 下拉刷新,下拉加载更多

### SmartRefreshLayout

https://github.com/scwang90/SmartRefreshLayout

## 列表

### BaseRecyclerViewAdapterHelper

https://github.com/CymChad/BaseRecyclerViewAdapterHelper

## 工具类

### AndroidUtilCode

https://github.com/Blankj/AndroidUtilCode

### DevUtils

 

https://github.com/afkT/DevUtils

 

 

## App更新

https://github.com/WVector/AppUpdate

https://github.com/javiersantos/AppUpdater

https://github.com/xuexiangjys/XUpdate

https://github.com/MZCretin/AutoUpdateProject



# 用户反馈



 

 

 

## 引导蒙层

https://github.com/binIoter/GuideView

https://github.com/huburt-Hu/NewbieGuide

https://github.com/qiushi123/GuideView-master

 

## 首次引导页

https://github.com/jinht/GuidePages

https://github.com/LuckSiege/AppWhenThePage

https://github.com/bingoogolapple/BGABanner-Android

 

## 权限

https://github.com/zengcanxiang/AndroidQStorage

## 定位

 

 

 

 

 

# 系统ui组件

## Dialog

https://github.com/hss01248/DialogUtil

https://github.com/mylhyl/Android-CircleDialog : ios风格

 

## Toast

https://github.com/hss01248/Toasty

## Notification

https://github.com/hss01248/NotifyUtil

 

## Snackbar

https://github.com/HuanHaiLiuXin/SnackbarUtils

https://github.com/johnkil/Android-AppMsg

https://github.com/MrEngineer13/SnackBar

 

## Popup window

### XPopup

 

https://github.com/li-xiaojun/XPopup

 

功能强大，UI简洁，交互优雅的通用弹窗！可以替代Dialog，PopupWindow，PopupMenu，BottomSheet，DrawerLayout，Spinner等组件，自带十几种效果良好的动画， 支持完全的UI和动画自定义！

### BasePopup

https://github.com/razerdp/BasePopup

 

 

 

## 选择器

### Android-PickerView(推荐)

https://github.com/Bigkoo/Android-PickerView

### android-pickers

https://github.com/addappcn/android-pickers

安卓选择器类库，包括日期及时间选择器（可设置范围）、单项选择器（可用于性别、职业、学历、星座等）、城市地址选择器（分省级、地级及县级）、数字选择器（可用于年龄、身高、体重、温度等）等……可以切换不同的模式（目前有普通模式，3d滚轮模式） [http://addapp.cn](http://addapp.cn/)

### 日历CalenderView

https://github.com/huanghaibin-dev/CalendarView

 

 

 

## Loading

Drawable和view

https://github.com/ybq/Android-SpinKit

带结束状态

https://github.com/ForgetAll/LoadingDialog

带进度:

https://github.com/peng8350/LoadingProgress

高度解耦，样式全部通过工厂类决定

https://github.com/xiandanin/LoadingBar

 

https://github.com/PangHaHa12138/Loading

 

## Banner

https://github.com/youth5201314/banner

 

# Textview

## 全方位增强

https://github.com/lygttpod/SuperTextView

https://github.com/chenBingX/SuperTextView

-keep class com.coorchice.library.gifdecoder.JNI { *; }

 

 

## 两边排版对齐

https://github.com/androiddevelop/AlignTextView

 

https://github.com/ufo22940268/android-justifiedtextview

 

## 限定行数后自动缩放大小

https://github.com/grantland/android-autofittextview

 

## 展开和收起

https://github.com/freecats/TextViewExpandableAnimation

![img](private/var/folders/2d/h_qly1js0lqb18sml00by7480000gn/T/com.kingsoft.wpsoffice.mac/wps-hss/ksohtml/wpsYv9B9J.jpg) 

https://github.com/bravoborja/ReadMoreTextView

 

 

 

 

## 滚动和跑马灯

仿淘宝首页热点新闻滚动，类中奖滚动，自动滚动文字、View、跑马灯

https://github.com/leiyun1993/AutoScrollLayout

 

## 竖向排列和对齐

https://github.com/devilist/AdvancedTextView

 

 

 

## 炫酷显示动画

https://github.com/hanks-zyh/HTextView

 

加载html

 

https://github.com/SufficientlySecure/html-textview

 

 

# 角标

https://github.com/HeZaiJin/SlantedTextView

![img](private/var/folders/2d/h_qly1js0lqb18sml00by7480000gn/T/com.kingsoft.wpsoffice.mac/wps-hss/ksohtml/wpseAcOpi.jpg) 

 

# 红点

![img](private/var/folders/2d/h_qly1js0lqb18sml00by7480000gn/T/com.kingsoft.wpsoffice.mac/wps-hss/ksohtml/wpsi9Xf29.jpg) 

https://github.com/qstumn/BadgeView

![img](private/var/folders/2d/h_qly1js0lqb18sml00by7480000gn/T/com.kingsoft.wpsoffice.mac/wps-hss/ksohtml/wpsBSwQwh.jpg) 

https://github.com/bingoogolapple/BGABadgeView-Android

 

![img](private/var/folders/2d/h_qly1js0lqb18sml00by7480000gn/T/com.kingsoft.wpsoffice.mac/wps-hss/ksohtml/wpsalpDKq.jpg) 

https://github.com/matrixxun/MaterialBadgeTextView

 

## 不同Launcher添加桌面角标

https://github.com/lixiangers/BadgeUtil

 

 

 

 

# Edittext

 

## TextFieldBoxes

![img](private/var/folders/2d/h_qly1js0lqb18sml00by7480000gn/T/com.kingsoft.wpsoffice.mac/wps-hss/ksohtml/wpsBj1QP1.jpg) 

https://github.com/HITGIF/TextFieldBoxes

 

## MaterialEditText

![img](private/var/folders/2d/h_qly1js0lqb18sml00by7480000gn/T/com.kingsoft.wpsoffice.mac/wps-hss/ksohtml/wpsJdC5CS.jpg) 

https://github.com/rengwuxian/MaterialEditText

## ClearEditText

https://github.com/MrFuFuFu/ClearEditText

 

## AutosizeEditText:自动增加行数

 

https://github.com/txusballesteros/AutosizeEditText

 

 

 

 

 

# 按钮

https://github.com/kyleduo/SwitchButton

 

 

 

# 悬浮

https://github.com/goweii/AnyLayer

 

 

 

 

 

 

 

 

### RWidgetHelper

圆角，边框，Gradient背景渐变，控件State各个状态UI样式，阴影，水波纹

RTextView,REditText,RLinearLayout / RRelativeLayout / RFrameLayout / RView / RConstraintLayout,RRadioButton / RCheckBox,RImageView

 

https://github.com/RuffianZhong/RWidgetHelper

 

 

# 键盘相关

## 仿微信键盘

https://github.com/zuiwuyuan/WeChatPswKeyboard

 

## 安全键盘

### 随机数字键盘

https://github.com/kuangch/custom-keyboard

https://github.com/onlyloveyd/LazyKeyboard

 

# 布局组件/layout

## Viewpager

可变高度

https://github.com/JmStefanAndroid/Mu5ViewPager

 

### 可复用:

https://github.com/zhuguohui/HorizontalPage

 

## Indicator

https://github.com/hackware1993/MagicIndicator

 

## Bottomtab

https://github.com/tyzlmjj/PagerBottomTabStrip

## Layout

### flexbox

https://github.com/google/flexbox-layout

### 动态化布局

https://github.com/alibaba/Tangram-Android

 

## 流式布局

![img](private/var/folders/2d/h_qly1js0lqb18sml00by7480000gn/T/com.kingsoft.wpsoffice.mac/wps-hss/ksohtml/wpsKPz9Ly.jpg) 

https://github.com/nex3z/FlowLayout

 

![img](private/var/folders/2d/h_qly1js0lqb18sml00by7480000gn/T/com.kingsoft.wpsoffice.mac/wps-hss/ksohtml/wpsdjOQ2M.jpg) 

 

https://github.com/LRH1993/AutoFlowLayout

 

 

 

 

 

# 特效

## 动画

https://github.com/daimajia/AndroidViewAnimations

## Behavior

https://github.com/JmStefanAndroid/EasyBehavior

## 阴影

https://github.com/lihangleo2/ShadowLayout

https://github.com/meetsl/SCardView-master

https://github.com/zhengcx/ShadowHelper

https://github.com/harjot-oberai/MaterialShadows

 

## 频道和移动

https://github.com/chengzhicao/ChannelView

 

 

 

 

## 点赞按钮礼炮

https://github.com/ChadCSong/ShineButton

 

## 圆角

Cardview

 

 

 

 

# Webview

## Agentweb

https://github.com/Justson/AgentWeb

https://github.com/lzyzsd/JsBridge

 

 

 

 

# 特效

## 阴影

https://github.com/lihangleo2/ShadowLayout

# Debug工具

## 抓包,数据库,ui全套

https://github.com/hss01248/flipperUtil

## fps显示

https://github.com/SilenceDut/fpsviewer

## 方法耗时和出入参

https://github.com/Vinctor/Uatu

 

 

 

 

 

# 效率工具

##  文案生成

https://github.com/hss01248/stringsxmlgenerator

 

## Selector

https://github.com/tianzhijiexian/SelectorInjection

## 前后台切换

 

## 网络状态变更

https://github.com/pwittchen/ReactiveNetwork

 

## 键盘弹出和隐藏监听

https://github.com/yescpu/KeyboardChangeListener

 

## 键盘挡住了输入框

https://github.com/xiewenfeng/SoftboradBlockEdittext

 

# 屏幕适配相关

## 界面通用适配

https://github.com/JessYanCoding/AndroidAutoSize

 

## textview自动缩小文字

https://github.com/grantland/android-autofittextview

 

## 多textview自动换行

flexbox

 

 

 

 

 

 

 

# 多媒体

## 多媒体元数据

https://github.com/xiandanin/MediaMetadataRetrieverCompat

## 视频播放

https://github.com/Doikki/DKVideoPlayer

 

 

# 换肤

https://github.com/hackware1993/injor

 

# Aop:

## XAOP

https://github.com/xuexiangjys/XAOP

 

支持快速点击切片@SingleClick，支持设置快速点击的时间间隔。

 

支持动态申请权限切片@Permission，支持自定义响应动作。

 

支持主线程切片@MainThread。

 

支持IO线程切片@IOThread，支持多种线程池类型。

 

支持日志打印切片@DebugLog，支持自定义日志记录方式。

 

支持内存缓存切片@MemoryCache，支持设置缓存大小。

 

支持磁盘缓存切片@DiskCache，支持自定义磁盘缓存，缓存有效时间等。

 

支持自动捕获异常的拦截切片@Safe，支持设置自定义异常处理者。

 

支持自定义拦截切片@Intercept，支持自定义切片拦截。

 

兼容Kotlin语法。

 

支持androidx。

 

## 防重复点击:

 

 

https://github.com/zhujiang521/AndroidAOP

 

## AopArms

 

https://sea-region.github.com/aicareles/AopArms

 

## SAF-AOP

 

https://sea-region.github.com/fengzhizi715/SAF-AOP



# hacker

https://github.com/CreditTone/hooker 基于frida实现的逆向工具包 

 

 

 

 

 

 

 

 

 

 

 