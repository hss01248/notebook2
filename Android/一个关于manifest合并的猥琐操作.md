# Android里模块化开发时,各类文件能模块化到哪一步

# 一个关于manifest合并的猥琐操作

> 背景

谷歌商店对声明在networkconfig里的<base-config cleartextTrafficPermitted="true">明文http开关会给予警告,但不会警告直接声明在manifest.xml里的application节点的android:usesCleartextTraffic="true"

我们需要在debug时开启networkconfig, release时不要配置networkconfig. 那么可以这样做:

![image-20220119113006713](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac/1642563012429-image-20220119113006713.jpg)

可以看到最终合并的manifest里同时有两个

![image-20220119113134482](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac/1642563094521-image-20220119113134482.jpg)

而release打包则没有对应的networkconfig,只有usercleartext

![image-20220119114318506](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac/1642563798578-image-20220119114318506.jpg)