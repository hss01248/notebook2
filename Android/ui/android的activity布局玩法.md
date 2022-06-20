# android的activity布局玩法



# 布局层级

https://github.com/Petterpx/FloatingX

![Activity-setContentView](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac2/1655704972213-68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f3030386933736b4e6c7931677232306b73373738306a3330726330693564696d2e6a7067.jpeg)

利用flipper查看布局,可见:

![image-20220620140337344](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac2/1655705017377-image-20220620140337344.jpg)

最后两个view,真正就只是背景,而不是真正的状态栏,导航栏view:

修改背景可见:

![image-20220620140648914](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac2/1655705208940-image-20220620140648914.jpg)

![image-20220620140723309](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac2/1655705243334-image-20220620140723309.jpg)

且onResumed时还不能立刻拿到

![image-20220620141127128](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac2/1655705487161-image-20220620141127128.jpg)

# 玩法1: debug包时显示activity名字

注册activityLifecycleCalback,在onActivityCreated回调里:

```java
    private void showActivityNameAtDecorView2(Activity activity) {
        View decorView = activity.getWindow().getDecorView();
        //extends FrameLayout
        if(decorView instanceof FrameLayout){
            FrameLayout root = (FrameLayout) decorView;
            TextView textView = new TextView(activity);
            //textView.setId(R.id.tv_text_debug);
            int padding = SizeUtils.dp2px(25);
            textView.setTextColor(Color.BLUE);
            textView.setTextSize(12);
            textView.setText( activity.getClass().getSimpleName());
            textView.setPadding(padding,padding,padding,padding);
            root.addView(textView);
        }
    }
```

