# Kotlin导论

# 背景

2011年7月JetBrains公布**Kotlin**项目，此时其已经研发了1年多了。
据说其名称来源于一个俄罗斯的城市圣彼得堡附近的一个岛屿

![img](https://gitee.com/hss012489/picbed/raw/master/picgo/1615778725002-v2-034cc047be22fd4ce6645208e550f171_720w.jpg)

2012年2月JetBrains将其开源了，开源协议采用的是 **Apache2。**

2016年2月Kotlin v1.0发布，我们知道软件一般到1.0就说明基本稳定了，准备好用于生产环境了，JetBrains承诺对其进行长期维护及向后兼容

2017年是Kotlin**逆袭元年**，1月Spring官方宣布在Spring5中支持Kotlin

在当年5月Kotlin迎来了其屌丝逆袭的高光时刻，Google在当年度的IO大会上宣布Kotlin为Android的First-Class支持的语言。但是Google为了安抚Java的拥趸们对外公开说Kotlin与Java地位相同，而且新功能及文档会优先支持Java.


2018年10月，Kotlin发布了v1.3版本引入了**协程**，持续向Java发起进攻。

2019年5月，Google宣布Kotlin为Android开发的优先语言.  打自己的脸了。所以说商业公司的话就是个屁，一切均以其利益最大化为准。

2020年8月Kotlin v1.4发布，完善跨平台编程技术，一段代码既可以给Android使用也可以给IOS使用。



Kotlin主要目标平台是JVM，专注挖Java墙脚，但是其野心不止于此其陆续推支持了JavaScript，Native，Multiplatform等平台。而且Kotlin还可以写脚本，现在连Gradle都官方支持了Kotlin脚本，所以说Kotlin不仅挖了Java的墙角，连Groovy的墙角它也挖



### TIOBE 编程语言排行榜排名-2021.03月

![image-20210315112204310](https://gitee.com/hss012489/picbed/raw/master/picgo/1615778530887-image-20210315112204310.jpg)

# 为什么要学?

## 语言本身优势

* 语法糖,简化书写
* [扩展属性和扩展函数](https://www.cnblogs.com/nicolas2019/p/10932131.html)
* **空指针安全**
* 支持协程
* 内联能力
* 基于jvm,完美利用java生态
* ...

## 外部力量

google  Android首选语言   

https://developer.android.com/

![image-20210316101129556](https://gitee.com/hss012489/picbed/raw/master/picgo/1615860689601-image-20210316101129556.jpg)

Jetbrains 

开源库生态



# kotlin能做什么?

> 基于jvm,能编译为js,二进制文件,机器学习也不是不能做.
>
> 主要工作是挑战java老大哥,同时也调戏javascript,挑逗c/c++,swift. 

![image-20210315141158514](https://gitee.com/hss012489/picbed/raw/master/picgo/1615788718568-image-20210315141158514.jpg)

>  野心: 全平台/跨平台. 
>
> 真正的一次编写,到处运行,还不需要jvm的支持.

Code written in common Kotlin works everywhere on all platforms

> 相对于flutter,RN的区别: 自己就具有原生/系统本身api的调用能力,而不用桥接. 或者桥接层可以也用kotlin完成.

![image-20210315141241187](https://gitee.com/hss012489/picbed/raw/master/picgo/1615788761218-image-20210315141241187.jpg)

## Android里它能做的:

* 写代码.   as支持+ktx套件
* 写gradle插件  :  groovy是动态语言,在插件module里没有代码提示,非常蛋疼
* 代替groovy编写编译脚本: 支持一般般. 目前脚本里groovy的提示足够智能,而且能与java**混写**.

#### [Android KTX](https://developer.android.com/kotlin/ktx)

Android KTX是包含在 `Android Jetpack` 及其他 `Android` 库中的一组 `Kotlin` 扩展程序。KTX 扩展程序可以为 `Jetpack`、`Android` 平台及其他 API 提供简洁而惯用的 `Kotlin`代码

![image-20210316095923132](https://gitee.com/hss012489/picbed/raw/master/picgo/1615859963178-image-20210316095923132.jpg)

![image-20210316100022869](https://gitee.com/hss012489/picbed/raw/master/picgo/1615860022913-image-20210316100022869.jpg)



# 工具链-语言生态

> 核心: 宇宙最强IDE厂商jetbrains所开发的语言. IDEA和Android studio提供全面支持.

* Java代码可以一键转Kotlin
* 查看kotlin代码对应的java代码写法

### android工程java项目如何转kotlin

[android工程java项目如何转kotlin](https://zhuanlan.zhihu.com/p/259968157)

右键:

![image-20210316141950229](https://gitee.com/hss012489/picbed/raw/master/picgo/1615875590286-image-20210316141950229.jpg)

![image-20210316142027444](https://gitee.com/hss012489/picbed/raw/master/picgo/1615875627493-image-20210316142027444.jpg)

![image-20210316142142141](https://gitee.com/hss012489/picbed/raw/master/picgo/1615875702187-image-20210316142142141.jpg)



转换前后对比:

![image-20210316143502335](https://gitee.com/hss012489/picbed/raw/master/picgo/1615876502383-image-20210316143502335.jpg)

![image-20210316143404864](https://gitee.com/hss012489/picbed/raw/master/picgo/1615876444915-image-20210316143404864.jpg)

# 如何学习一门编程语言?

>  重视语言特性,而不是语言

每一种语言里面必然有一套“通用”的特性。比如变量，函数，整数和浮点数运算，等等。这些是每个通用程序语言里面都必须有的，一个都不能少。你只要通过“某种语言”学会了这些特性，掌握这些特性的根本概念，就能随时把这些知识应用到任何其它语言.

- 变量定义
- 算术运算
- for 循环语句，while 循环语句
- 函数定义，函数调用
- 递归
- 静态类型系统
- 类型推导
- lambda 函数
- 面向对象
- 垃圾回收
- 指针算术
- goto 语句
- 模块系统

完全理解一种语言特性最好的方法就是自己动手实现它，也就是自己写一个解释器来实现它的语义.

系统工具性质的东西:比如正则表达式，Web 概念,io操作只是api包装,非语言特性,了解即可.

参考:[如何掌握所有的程序语言](https://www.yinwang.org/blog-cn/2017/07/06/master-pl)

# 语言特性

> 动态类型 vs 静态类型     

[知乎-弱类型、强类型、动态类型、静态类型语言的区别是什么？](https://www.zhihu.com/question/19918532)

[wiki-类型系统](https://zh.wikipedia.org/wiki/%E9%A1%9E%E5%9E%8B%E7%B3%BB%E7%B5%B1)

* 静态类型、动态类型: 指的**类型检查**发生的时机. 

  静态类型: 编译期检查类型.  便于工程化操作.  

  动态类型: 运行时才检查类型

* 显式类型(**声明**)=>你得说，不然编译器不知道。

  类型推断=>编译器很聪明，很多时候你不明说也能推导出来。

* 强类型(**定义**): 一旦变量被指定某个数据类型，如果不经强制转换，即永远是此数据类型.

  强类型定义语言带来的严谨性能够有效的避免许多错误.

  弱类型: 

  

**“语言是否动态”与“语言是否类型安全”之间是完全没有联系的！**

Python是动态语言，是强类型定义语言（类型安全的语言）;
VBScript是动态语言，是弱类型定义语言（类型不安全的语言）;
JAVA是静态语言，是强类型定义语言（类型安全的语言）

kotlin是静态语言,是强类型定义语言



思考:

为什么 js世界里,TypeScript大行其道? 

> 扩展属性/扩展方法: 

[扩展属性和扩展函数](https://www.cnblogs.com/nicolas2019/p/10932131.html)

类似js的便捷性:

![image-20210315162517256](https://gitee.com/hss012489/picbed/raw/master/picgo/1615796717298-image-20210315162517256.jpg)



扩展一个类的新功能而无需继承该类或者使用像装饰者这样的设计模式

你可以为一个你不能修改的、来自第三方库中的类编写一个新的函数。 这个新增的函数就像那个原始类本来就有的函数一样，可以用普通的方法调用。 这种机制称为 *扩展函数* 

![image-20210315163917614](https://gitee.com/hss012489/picbed/raw/master/picgo/1615797557660-image-20210315163917614.jpg)

![image-20210315163942227](https://gitee.com/hss012489/picbed/raw/master/picgo/1615797582265-image-20210315163942227.jpg)



![image-20210316095650744](https://gitee.com/hss012489/picbed/raw/master/picgo/1615859810806-image-20210316095650744.jpg)

> 函数默认值: 类似python. 例子同上



### 函数式编程特性

几乎所有语言都支持函数式编程

> **Lambda表达式**

`Lambda`表达式的本质其实是`匿名函数`，因为在其底层实现中还是通过`匿名函数`来实现的

```kotlin
{ a, b -> a.length < b.length }

相当于:
fun compare(a: String, b: String): Boolean = a.length < b.length




button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Log.d("TAG","按钮被点击,匿名内部类");
            }
        });


button.setOnClickListener(view-> Log.d(TAG,"按钮被点击,lambda表达式"));
```

* lambda表达式是一个表达式，而不是函数

  类里面减少了函数的数量，在读取和执行类的过程中都提高了性能。可以说lambda表达式是替代简单低阶函数的最佳选择

* 匿名函数可以指定返回类型。而kotlin可以自动推动返回类型，但无法指定返回类型.



> 高阶函数

一个函数就可以接收另一个函数作为参数，或者返回值是一个函数,这种函数就称之为高阶函数.

```js
function add(x, y, f) {
    return f(x) + f(y);
}


add(-5, 6, Math.abs);
```



类比java:   

**在java里,方法必须被一个类承载:** 

一个方法的某个参数是一个接口.

类比rxjava:

所有的操作符都接受一个接口作为参数.



> 闭包: 匿名内部类对外部变量/外部类的引用

[Kotlin闭包](https://www.jianshu.com/p/fd24a37ae411)

![image-20210315152628357](https://gitee.com/hss012489/picbed/raw/master/picgo/1615793188398-image-20210315152628357.jpg)



### 其他的语法糖

# [Kotlin的语法糖们](http://qinghua.github.io/kotlin-syntax-suger/)

[Kotlin语法糖总结](https://www.jianshu.com/p/5f00a223bf84)



# 能力边界-槽点

* coroutine跟spring context, Jetbrains.Exposed, spring security不兼容，只有很少地方能用，就很闹心。
* 泛型/模板也是先天残废，传个泛型参数还要附送Type然后Reflection
* 函数式 debug麻烦,中间过程看不到
* Kotlin 只能保证纯 KT 代码的 **Null-safe** ,无法保证对 Java 的调用也没有空指针
* 牛逼的语法解决不了傻逼的业务和逻辑需求



# 最重要的: 练习,动手写

# 官方文档

[kotlin中文文档](https://www.kotlincn.net/docs/reference/)

[Android-kotlin开发者指南](https://developer.android.com/kotlin/first)

