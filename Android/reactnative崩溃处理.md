# ReactNative崩溃处理

# 痛点

每次reactnative各种崩溃直接导致整体app崩溃,导致崩溃率停留在千分之一级,不能达到万分之几级

# 源码

![企业微信截图_b485fa5b-1299-49f4-be83-6e83c6779bfc](https://cdn.jsdelivr.net/gh/hss01248/picbed@master/uPic/企业微信截图_b485fa5b-1299-49f4-be83-6e83c6779bfc.png)

![企业微信截图_c7a86f0a-3a72-4bf6-9f80-7fa2789aec44](https://cdn.jsdelivr.net/gh/hss01248/picbed@master/uPic/企业微信截图_c7a86f0a-3a72-4bf6-9f80-7fa2789aec44.png)

## 当没有设置nativecallExceptionHandler时: 

![企业微信截图_24d8ae10-370c-4464-8fbc-71d55c499c1b](https://cdn.jsdelivr.net/gh/hss01248/picbed@master/uPic/企业微信截图_24d8ae10-370c-4464-8fbc-71d55c499c1b.png)

![企业微信截图_75f0ab86-25f3-48b5-9ea0-b3085f3cc42e](https://cdn.jsdelivr.net/gh/hss01248/picbed@master/uPic/企业微信截图_75f0ab86-25f3-48b5-9ea0-b3085f3cc42e.png)

![企业微信截图_f72d5393-3e1d-4f8d-a158-b187dd5c0c4c](https://cdn.jsdelivr.net/gh/hss01248/picbed@master/uPic/企业微信截图_f72d5393-3e1d-4f8d-a158-b187dd5c0c4c.png)

# 所以我们需要做的是:

初始化ReactNativeContext时,传入我们自己的NativeModuleCallExceptionHandler,在里面:

* 将exception上报到自己的统计平台,而不是抛出异常.根据异常类型,看是否需要结束RNActivity.
* 非正式包时,弹出一个界面展示exception给开发和测试看,便于debug

## 



