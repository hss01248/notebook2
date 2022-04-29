# webview被导航栏遮盖的处理

# 背景

webview所在activity使用[android-titlebar](https://github.com/wuhenzhizao/android-titlebar)来做沉浸式状态栏处理, 沉浸式处理的效果是导航栏这里处理有bug,webview会深入到导航栏下面. 即使将导航栏透明化处理,还是会有遮挡. 

尝试了给window设置flag,以及各种fitsystemWindows,clippToPaddings处理,依然不能兼容所有情况

后面打印各view的尺寸时发现,titlebar+webview的高度>activity的contentview的高度, 那么只要调整webview的高度即可:

# 处理:

```java
/**
     * 一个处理方式适配所有情况:
     * 1 普通页面进入
     * 2 url里带?hideTopBar=1进入-一进入就立刻让页面沉浸式状态栏+隐藏标题栏. 但导航栏不隐藏
     * 3 a标签+targetblank开启新窗口进入, 以及window.open(url)进入
     * 4 jsbridge动态控制标题栏显隐: showTopBar,hideTopBar, 以及老的交互jsShowTopBar,jsHideTopBar
     * 5 导航栏有缩回功能的情况,可以点击收起,或自动收起
     * @param activity
     */
    private void adjustWebviewAndNavi(Activity activity) {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT_WATCH) {
            LinearLayout content = activity.findViewById(R.id.ll_web_root);
            if(content != null){
                //据说是适配全面屏导航栏的,但辣鸡一个
                /*content.setOnApplyWindowInsetsListener(new View.OnApplyWindowInsetsListener() {
                    @Override
                    public WindowInsets onApplyWindowInsets(View v, WindowInsets insets) {
                        XLogUtil2.w("onApplyWindowInsets",insets);
                        //v.setPadding(v.getPaddingRight(),v.getPaddingTop(),v.getPaddingLeft(),insets.getSystemWindowInsetBottom());
                        return insets;
                    }
                });*/
            }
        }
        //事实证明,官方配置window的flags有各种坑,还是自己算比较靠谱
        activity.findViewById(android.R.id.content).addOnLayoutChangeListener(new View.OnLayoutChangeListener() {
            @Override
            public void onLayoutChange(View v, int left, int top, int right, int bottom, int oldLeft, int oldTop, int oldRight, int oldBottom) {
                if(titlebarHolder.mCommonTitleBar != null){
                    XLogUtil2.w("navi h 0",
                            "NavBarHeight:"+BarUtils.getNavBarHeight(),
                            BarUtils.isNavBarVisible(activity),
                            ScreenUtils.getAppScreenHeight(),
                            "fullscreenHeight:"+ScreenUtils.getScreenHeight(),
                            "contentView.getMeasuredHeight"+activity.findViewById(android.R.id.content).getMeasuredHeight(),
                            "webView.getMeasuredHeight:"+webView.getMeasuredHeight(),
                            "CommonTitleBar.getMeasuredHeight:"+titlebarHolder.mCommonTitleBar.getMeasuredHeight(),
                            "titlebarHolder.mCommonTitleBar.getVisibility():"+titlebarHolder.mCommonTitleBar.getVisibility());
                }else {
                    XLogUtil2.w("navi h 0",
                            "NavBarHeight:"+BarUtils.getNavBarHeight(),
                            BarUtils.isNavBarVisible(activity),
                            ScreenUtils.getAppScreenHeight(),
                            "fullscreenHeight:"+ScreenUtils.getScreenHeight(),
                            "contentView.getMeasuredHeight"+activity.findViewById(android.R.id.content).getMeasuredHeight(),
                            "webView.getMeasuredHeight:"+webView.getMeasuredHeight());
                }

                //      //ScreenUtils.getScreenHeight() 真正屏幕尺寸
                //         //BarUtils.getNavBarHeight() 导航栏高度,不管显示还是隐藏
                //         //BarUtils.isNavBarVisible(activity) 不准,不要用
                int contentHeight = activity.findViewById(android.R.id.content).getMeasuredHeight();
                int webviewHeight = webView.getMeasuredHeight();
                if(webviewHeight > contentHeight){

                }
                if(titlebarHolder.mCommonTitleBar != null){
                    if(titlebarHolder.mCommonTitleBar.getVisibility() != View.GONE){
                        webviewHeight = webviewHeight + titlebarHolder.mCommonTitleBar.getMeasuredHeight();
                    }
                }
                if(webviewHeight > contentHeight){
                    XLogUtil2.w("navi h 3","检测到webview+titlebar大于contentview高度,此时webview嵌入到导航栏内部,被覆盖,尝试控制webview高度");
                    ViewGroup.LayoutParams layoutParams = webView.getLayoutParams();
                    layoutParams.height = webView.getMeasuredHeight() - (webviewHeight - contentHeight);
                    webView.setLayoutParams(layoutParams);
                }else if(webviewHeight < contentHeight){
                    XLogUtil2.w("navi h 3","检测到webview+titlebar小于contentview高度,此时webview下方有空白区域,需要拉伸webview高度");
                    ViewGroup.LayoutParams layoutParams = webView.getLayoutParams();
                    layoutParams.height = webView.getMeasuredHeight() + (contentHeight - webviewHeight);
                    webView.setLayoutParams(layoutParams);
                }else {
                    XLogUtil2.i("navi h 3","webview高度符合预期");
                }
            }
        });
    }
```



