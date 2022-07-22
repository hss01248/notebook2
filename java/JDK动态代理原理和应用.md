# JDK动态代理原理和应用

## 代理在现实商业里的应用

[渠道、终端、连锁、加盟、代理、分销、代销分别是什么意思，有什么不同？](https://www.zhihu.com/question/20908623)

## 动态代理的基本使用

代码演示:

1 基本使用

2 封装通用的切面层 ,以及与范型的结合使用

可以传入任意接口的实现类:(需要泛型加到类声明上)

![image-20220217172357572](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac/1645089839315-image-20220217172357572.jpg)

![image-20220217172517657](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac/1645089919506-image-20220217172517657.jpg)

也可以泛型直接声明在方法上:

![image-20220217172059368](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac/1645089666991-image-20220217172059368.jpg)

以上两者均需要:

代理调用时只调用接口的方法.不能调用类有而接口没有的方法

![image-20220217172838973](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac/1645090120292-image-20220217172838973.jpg)



## 代理类是什么样的?

动态代理生成的是个什么类?

## 手写实现动态代理

[纯手写实现JDK动态代理](https://cloud.tencent.com/developer/article/1532540)

* 手动拼字符串,用StringBuilder写一个代理类
* 将字符串的类编译成字节码class
* 用自定义的classloader加载这个字节码



## 应用

日志

LogProxy.java

任意通用型切面功能

## 局限性/功能边界

只能代理接口.不能代理类.->JAVA不能多继承

生成的代理方法,代理类上,注解被抹除了

getClass().getInterfaces()只能获取当前类自己实现的接口,不能父类实现的接口: 需要递归获取所有接口

