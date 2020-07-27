# JVM一图流

[TOC]



# 总纲

![Java Virtual Machine architecture diagram ](https://javatutorial.net/wp-content/uploads/2017/10/jvm-architecture-992x1024.png)



## 类手绘版:

![img](https://cdn.jsdelivr.net/gh/hss01248/picbed/pic/1742604-20190923191146586-1872066162.png)

## 全流程

![img](https://pic3.zhimg.com/80/v2-d6c72796b96a774de57f160c7a31d53f_1440w.jpg)

# classLoader

## class文件结构

https://zhuanlan.zhihu.com/p/149876413

https://my.oschina.net/u/2246410/blog/1800670

![image-20200726112348450](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200726112348450.png)

![preview](https://pic1.zhimg.com/v2-d627f33b6e850bc2efe0fac63c241280_r.jpg)



## 双亲委托加载机制

![img](https://cdn.jsdelivr.net/gh/hss01248/picbed/pic/20160117130521757)

![img](https://cdn.jsdelivr.net/gh/hss01248/picbed/pic/20160119193940660)

## 对象在内存中的结构

### 简略:

![img](https://cdn.jsdelivr.net/gh/hss01248/picbed/pic/1007277-20190220160414249-744349570.jpg)

### 详细

![image](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy83MDE3MTQwLTgwMzc0ZTY1YzZiMjBjYmU)

![img](https://pic4.zhimg.com/v2-4086c030d0dd3b82a7727eff4b05e833_b.jpg)

# Runtime Data Area

主要参考:

https://blog.csdn.net/u010349169/article/details/40043991

## 整体结构

![img](https://pic2.zhimg.com/80/v2-05ff30bb26d3888276de92c0031dd069_1440w.jpg)

![img](https://cdn.jsdelivr.net/gh/hss01248/picbed/pic/20141013141216040)

## 栈

![img](https://cdn.jsdelivr.net/gh/hss01248/picbed/pic/20141117164047783)

## 栈帧

![img](https://cdn.jsdelivr.net/gh/hss01248/picbed/pic/20141013142638909)

## 方法区

![img](https://cdn.jsdelivr.net/gh/hss01248/picbed/pic/20141013142924359)

## 堆

![img](https://oos-qiniu.sudo.ren/attachment/20191119/4ddcd69ad10947eebe9546d627af2fcc.jpg)

# Execution Engine

## JIT

## GC

### 引用相关

![WeChatWorkScreenshot_5d441425-a469-411d-b8f2-767e9eb67385](https://cdn.jsdelivr.net/gh/hss01248/picbed/pic/WeChatWorkScreenshot_5d441425-a469-411d-b8f2-767e9eb67385.png)

![Image](https://cdn.jsdelivr.net/gh/hss01248/picbed/pic/Image.png)

### 算法

https://baijiahao.baidu.com/s?id=1632054498996744393&wfr=spider&for=pc
https://zhuanlan.zhihu.com/p/106707595

![image-20200726111507098](https://cdn.jsdelivr.net/gh/hss01248/picbed/pic/image-20200726111507098.png)

#### 分代回收算法:

分代回收算法https://zhuanlan.zhihu.com/p/84338255

![image-20200726111648848](https://cdn.jsdelivr.net/gh/hss01248/picbed/pic/image-20200726111648848.png)



# JNI

![Figure 7-1.](https://media.springernature.com/lw785/springer-static/image/chp%3A10.1007%2F978-1-4302-6131-5_7/MediaObjects/978-1-4302-6131-5_7_Fig1_HTML.jpg)

![Image for post](https://miro.medium.com/max/3143/1*3jnCbOPGbR1U5IN5G1i7-Q.png)





# 相关博客

## jvm图解-经典

https://blog.csdn.net/u010349169/category_2630089.html

https://blog.csdn.net/csdnliuxin123524/article/details/81303711



