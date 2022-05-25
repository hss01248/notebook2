# webview和js的一些疑难问题

# 1 在shouldInterceptRequest方法里给window设置属性,为什么页面里拿不到这个属性?



![image-20220525162051049](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac2/1653466851087-image-20220525162051049.jpg)

![image-20220525162120287](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac2/1653466883225-1653466880316-image-20220525162120287.jpg)

![image-20220525162816026](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac2/1653467296053-image-20220525162816026.jpg)

![image-20220525162518414](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac2/1653467118443-image-20220525162518414.jpg)



在shouldInterceptRequest方法里,其window对象是前一个页面

如果是第一次进入,那么就是blank

onPageStated方法中,js方法里拿到的window才是当前的window

> 所以给window设置属性,应该在onPageStated()方法内设置

console上打印为: 

![image-20220525161925508](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac2/1653466770786-image-20220525161925508.jpg)

![image-20220525163001563](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac2/1653467401590-image-20220525163001563.jpg)

# 2 shouldInterceptRequest方法默认是在同一个线程里执行,怎么样能像chrome一样并发执行?



> 是误解,只是线程名一样,线程id不一样,实际上是并发执行的,不是同一个线程.
>
> 只是同一个域名下,线程数有限制,不会开无线多个

![image-20220525164710521](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac2/1653468430555-image-20220525164710521.jpg)

翻看一些blog,都TM在扯淡

比如

https://juejin.cn/post/6844903497494691848

https://www.bianchengquan.com/article/323537.html