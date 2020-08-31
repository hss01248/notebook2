# kotlin

# 与java对比:

[kotlin vs java](https://github.com/MindorksOpenSource/from-java-to-kotlin/blob/master/README-ZH.md)s

# class操作

![这里写图片描述](http://hss01248.tech/uPic/2020-08-05-16-24-07-70.png)

![这里写图片描述](http://hss01248.tech/uPic/2020-08-05-16-24-26-70-20200805162426450.png)



# 类例子

![image-20200805162634594](http://hss01248.tech/uPic/2020-08-05-16-26-39-2020-08-05-16-26-34-image-20200805162634594.png)

# 匿名内部类

https://blog.csdn.net/hexingen/article/details/72824084

```java
 /**
     * 采用对象表达式来创建接口对象，即匿名内部类的实例。
     */
    instance.setInterFace(object : TestInterFace {
        override fun test() {
            println("对象表达式创建匿名内部类的实例")
        }
    })
```



```java
class Test {
    var v = "成员属性"

    /**
     * 一个在类中嵌套的类
     * 引用不到外层嵌套类的成员
     */
    class Nested {
        fun nestedTest() {
            println("类可以嵌套其他类中")
        }
    }
    /**
     * inner标记一个类是内部类
     * 可以引用外部类的成员
     * 采用this@类名方式，获取到外部类的this对象
     */
    inner class Inner {
        fun innerTest() {
            var t = this@Test //获取外部类的成员变量
            println("内部类可以引用外部类的成员，例如：" + t.v)
        }
    }
    fun setInterFace(test: TestInterFace) {
        test.test()
    }
}
```



# 静态方法,工具类

https://blog.csdn.net/xuyonghong1122/article/details/80268981

![image-20200819101406991](http://hss01248.tech/uPic/2020-08-19-10-14-10-image-20200819101406991.png)

![image-20200805163412986](http://hss01248.tech/uPic/2020-08-05-16-34-13-image-20200805163412986.png)

![image-20200805163506097](http://hss01248.tech/uPic/2020-08-05-16-35-06-image-20200805163506097.png)