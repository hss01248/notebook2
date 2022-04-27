# activity-fragment-view的回调和日志



# activity回调日志

```java
application.registerActivityLifecycleCallbacks(new LogActivityCallback())//每个回调里打日志即可
```

# fragment回调日志

在上面的ActivityLifecycleCallbacks里的onActivityCreated(activity,instance)回调里,

调用:

```java
if(!(activity instanceof FragmentActivity)){
            return;
}
FragmentActivity fragmentActivity = (FragmentActivity) activity;
fragmentActivity.getSupportFragmentManager().registerFragmentLifecycleCallbacks(new LogFragmentCalback(),true);
```



# view的生命周期:

> 一图流



![mmexport1650939164793](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac/1651062844514-mmexport1650939164793.jpg)

> view的onWindowFocusChanged基本上=activity的onResume 和onPause.
>
> 且onWindowFocusChanged在onDraw后执行,对于静态view,可以用来getMesuredWith()获取宽高.

当然,如果要感知外层activity或fragment的生命周期,最好还是使用lifecycle组件.  

依赖

```groovy
  api 'androidx.lifecycle:lifecycle-common-java8:2.2.0'
```

view上实现:

```java
implements DefaultLifecycleObserver
```

然后愉快玩耍