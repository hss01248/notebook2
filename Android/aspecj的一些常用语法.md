# aspectj的一些常用语法



# 切入方法执行

## 切入接口的实现类的方法执行

```java
execution(* com.facebook.react.bridge.Promise.*(..))
```

## 切入某个类的所有子类的某个方法的执行

[aspectJ对于继承类的方法捕获call(Signature)——能够自动处理](https://blog.csdn.net/wild46cat/article/details/52154064)





# 阅读

[关于 Spring AOP (AspectJ) 你该知晓的一切](https://juejin.cn/post/6844903726344323085)

