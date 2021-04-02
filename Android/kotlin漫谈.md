# kotlin漫谈

> 具体使用入门/细节直接看官方文档,此文只谈对kotlin语言特性/语法糖的一些感受.

一些语言特性可以结合编译生成的字节码来理解

![image-20210402094803485](https://gitee.com/hss012489/picbed/raw/master/picgo/1617328083534-image-20210402094803485.jpg)

或者使用反编译为java的形式查看. 

![image-20210402094956801](https://gitee.com/hss012489/picbed/raw/master/picgo/1617328196828-image-20210402094956801.jpg)



![image-20210402095137905](https://gitee.com/hss012489/picbed/raw/master/picgo/1617328297928-image-20210402095137905.jpg)



### 扩展函数的本质

>  调用时代码写法是对象.扩展(),实际编译成静态方法,被扩展对象本身(this)作为第一个参数传入.

![image-20210402100051345](https://gitee.com/hss012489/picbed/raw/master/picgo/1617328851371-image-20210402100051345.jpg)



### 子类父类对象创建时初始化顺序

> 没必要记,直接看字节码就行了



```kotlin
// demo for init 
open class DadConstructor(attr1: String){

    val firstProperty = "dad First property: $attr1".also(::println)

    init {
        println("dad init block 1")
    }

    val secondProperty = " dad Second property: $attr1".also(::println)

    init {
        println("dad  init block 2")
    }

    constructor() : this("erdai666"){
        println(" dad second constructor")
    }
}

class SonConstructor : DadConstructor{

    constructor() : super(){
        println("son second constructor")
    }

    init {
        println("son init block1")
    }
}

fun main() {
    SonConstructor()
}
```



![image-20210402101353008](https://gitee.com/hss012489/picbed/raw/master/picgo/1617329633042-image-20210402101353008.jpg)



对应的方法:

先父类init方法,然后子类init代码块,然后子类对应的构造函数.

![image-20210402101702105](https://gitee.com/hss012489/picbed/raw/master/picgo/1617329822140-image-20210402101702105.jpg)



其最先调用父类的空init方法:

![image-20210402102330889](https://gitee.com/hss012489/picbed/raw/master/picgo/1617330210922-image-20210402102330889.jpg)





从父类主init方法的字节码可以看出,一个对象初始化时,构造函数,变量,init块的初始化顺序:

![image-20210402102032554](https://gitee.com/hss012489/picbed/raw/master/picgo/1617330032587-image-20210402102032554.jpg)

![image-20210402102048159](https://gitee.com/hss012489/picbed/raw/master/picgo/1617330048193-image-20210402102048159.jpg)

![image-20210402102115659](https://gitee.com/hss012489/picbed/raw/master/picgo/1617330075690-image-20210402102115659.jpg)



![image-20210402102154160](https://gitee.com/hss012489/picbed/raw/master/picgo/1617330114190-image-20210402102154160.jpg)



### 序列

和python的yield关键字提供的生成器功能是一个道理.

###  密封类 vs 枚举

> 都是类型和有限个子类/对象都声明在同一个类/文件中

密封类: 子类有限

枚举: 对象有限. 只能是已声明的这些对象,且对象为单例.

### 委托

https://www.kotlincn.net/docs/reference/delegation.html

* 委托类: 跟java的代理一样,只是在已知被代理类型时可以简化写法->直接在语言层面提供支持

* 委托属性: 具有了类似js mvvm框架一样的对属性的切面功能,可以观测,拦截属性的变化. 

  比如vue的计算属性:

  ```js
  new Vue({
    data: {
      count: 10，
      blog:{
          title:'my-blog',
          categories:[]
      }
    },
    computed: {
      categories() {
        return this.blog.categories;
      }
    },
    watch: {
      categories(newVal, oldVal) {
        console.log(`new:${newVal}, old:${oldVal}`);
      }, 
    },
  })
  ```

  在kotlin里使用委托属性来观察属性变化:

  ```kotlin
  class User {
      var name: String by Delegate.observable("<no name>"){
          prop , old , new -> 
          println("$old -> $new")
      }
  }
  
  fun main(args: Array<String>){
      val user = User()
      user.name = "first"
      user.name = "second"
  }
  
  //output:
  //<no name> -> first
  //first -> second
  ```

  