# Rxjava



# 根本原理

> 自己写代码实现rxjava,怎么做? 如何实现: 链式,线程切换,各操作符? 一个类+两个接口即可演示完核心原理:

![image-20210111194924736](https://gitee.com/hss012489/picbed/raw/master/picgo/1610365764919-image-20210111194924736.jpg)

> 参考视频教程: https://www.bilibili.com/video/BV19h411172f?p=2
>
> demo代码: https://github.com/hss01248/aop-android/tree/master/rxjavademo/src/main/java/com/hss01248/rxjavademo

```java
public interface MyFunc<R,T> {
    R apply(T t);
}

public interface MyObserver<T> {

    void onNext(T t);

    void onError(Throwable throwable);

    void onComplete();
}
```

## 类:

### 定义:

```java
public abstract class MyObservable<T> {

    public  abstract void subscrib(MyObserver<T> observer);
 }
```

### 链式调用之创建:

```java
public static <T> MyObservable<T> create(MyObservable<T> observable){
    return observable;
}
```

### 变换/Map:

```java
public <R> MyObservable<R>  map(MyFunc<R,T> func){
        return new MyObservable<R>() {
            @Override
            public void subscrib(MyObserver<R> observer) {
                MyObservable.this.subscrib(new MyObserver<T>() {
                    @Override
                    public void onNext(T t) {
                        //往下执行时,进行函数调用
                        R r = func.apply(t);
                        observer.onNext(r);
                    }

                    @Override
                    public void onError(Throwable throwable) {
                        observer.onError(throwable);
                    }

                    @Override
                    public void onComplete() {
                        observer.onComplete();
                    }
                });
            }
        };
    }
```



### 订阅时切换线程:

```java
/**
 * 切换上流线程示例
 * @return
 */
public MyObservable<T> subscribOnIO(){
  return  new MyObservable<T>(){
        @Override
        public void subscrib(MyObserver<T> observer) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    //往上订阅时切换线程
                    MyObservable.this.subscrib(observer);
                }
            }).start();
        }
    };
}
```



### 统一切换下游线程:

```java
public MyObservable<T> observerOnMainThread(){
       return new MyObservable<T>(){
            @Override
            public void subscrib(MyObserver<T> observer) {
                MyObservable.this.subscrib(new MyObserver<T>() {
                    @Override
                    public void onNext(T t) {
                        new Handler(Looper.getMainLooper()).post(new Runnable() {
                            @Override
                            public void run() {
                                //往下执行时切换线程
                                observer.onNext(t);
                            }
                        });
                    }

                    @Override
                    public void onError(Throwable throwable) {
                        //同上
                    }

                    @Override
                    public void onComplete() {
                        //同上
                    }
                });
            }
        };
    }

public MyObservable<T> observerOnBackThread(){
        return new MyObservable<T>(){
            @Override
            public void subscrib(MyObserver<T> observer) {
                MyObservable.this.subscrib(new MyObserver<T>() {
                    @Override
                    public void onNext(T t) {
                        new Thread(new Runnable() {
                            @Override
                            public void run() {
                                //往下执行时切换线程
                                observer.onNext(t);
                            }
                        }).start();
                    }

                    @Override
                    public void onError(Throwable throwable) {
                        //同上
                    }

                    @Override
                    public void onComplete() {
                        //同上
                    }
                });
            }
        };
    }
```



## 使用:

```java
 MyObservable.create(new MyObservable<String>() {
            @Override
            public void subscrib(MyObserver<String> observer) {
                Log.d("create:", "," + Thread.currentThread().getName());
                for (int i = 0; i < 10; i++) {
                    observer.onNext(i + "");
                }


            }
        }).map(new MyFunc<Integer, String>() {
            @Override
            public Integer apply(String s) {
                Log.d("map1:", "integer:" + s + "," + Thread.currentThread().getName());
                return Integer.parseInt(s) * 2;
            }
        })/*.subscribOnIO()
        .observerOnMainThread()*/
        .map(new MyFunc<String, Integer>() {
            @Override
            public String apply(Integer integer) {
                Log.d("map2:", "integer:" + integer + "," + Thread.currentThread().getName());
                return integer + "->String";
            }
        }).subscribOnIO()
        .observerOnMainThread()
        .subscrib(new MyObserver<String>() {
            @Override
            public void onNext(String s) {
                Log.d("onNext:", "finnaly:" + s + "," + Thread.currentThread().getName());
            }

            @Override
            public void onError(Throwable throwable) {
                throwable.printStackTrace();

            }

            @Override
            public void onComplete() {
                Log.d("onComplete:", "finnaly: onComplete");
            }
        });
```

Log:

![image-20210111195655731](https://gitee.com/hss012489/picbed/raw/master/picgo/1610366215787-image-20210111195655731.jpg)



### 将中间注释打开,也就是多次切换线程:

.subscribOnIO()
.observerOnMainThread()

![image-20210111200008825](https://gitee.com/hss012489/picbed/raw/master/picgo/1610366408879-image-20210111200008825.jpg)

### 子线程

```java
}).map(new MyFunc<Integer, String>() {
    @Override
    public Integer apply(String s) {
        Log.d("map1:", "integer:" + s + "," + Thread.currentThread().getName());
        return Integer.parseInt(s) * 2;
    }
}).subscribOnIO()//上面使用子线程
.observerOnMainThread()//后续使用主线程
.map(new MyFunc<String, Integer>() {
    @Override
    public String apply(Integer integer) {
        Log.d("map2:", "integer:" + integer + "," + Thread.currentThread().getName());
        return integer + "->String";
    }
})
.observerOnBackThread()//后续切到子线程
.subscrib(new MyObserver<String>() {
```



![image-20210111200259162](https://gitee.com/hss012489/picbed/raw/master/picgo/1610366579215-image-20210111200259162.jpg)



# 一些问题

## 工程化->如何保证使用Rxjava绝对不崩溃?

* 对MyObserver的onNext(),onComplete()进行try-catch, catch后调用onError()
* 如果onError里也崩溃呢? 对onError也try-catch包裹,提供一个全局处理异常的回调,供框架使用者初始化时调用.



## 链式调用流程中,Observable数量的变化方法:

* 一个Observable怎么样能在链式流中变成多个Observable?
* 上面已经变成的多个Observable怎么能够在下游组合成一个Observable?以及怎么组合成一次回调?



# 一些经典函数方法的实现:

//todo







# 学习资料/博客

https://github.com/Carson-Ho/RxJavaLearningMaterial

# 代码示例



# 学习Demo app

https://github.com/KunMinX/RxJava2-Operators-Magician

https://github.com/leeowenowen/rxjava-examples

https://github.com/xinghongfei/Hello-RxJava



# 开发工具

https://github.com/akaita/RxJava2Debug    exception中显示真正的原因

https://github.com/davidmoten/rxjava2-extras 各种transformers和工具

https://github.com/akarnokd/RxJavaExtensions

https://github.com/skyNet2017/frodo2  logging [RxJava](https://github.com/ReactiveX/RxJava) [Observables](http://reactivex.io/documentation/observable.html) and [Subscribers](http://reactivex.io/RxJava/javadoc/rx/Subscriber.html) outputs on the logcat





# Android开发套件

https://github.com/Piasy/RxScreenshotDetector



rxjava基于集合的并发

https://www.jianshu.com/p/1e4d194bb782





# 存储和缓存相关库

https://github.com/Gridstone/RxStore

https://github.com/VictorAlbertos/RxCache



# 视频

https://www.bilibili.com/video/BV1Wt411d7N2   Java编程方法论-响应式 之 Rxjava2源码设计实现解读 全集（已完结）

https://www.bilibili.com/video/BV1iT4y137PZ



![image-20200615225433728](https://cdn.jsdelivr.net/gh/hss01248/picbed/pic/image-20200615225433728.png)



![image-20200615225523090](https://cdn.jsdelivr.net/gh/hss01248/picbed/pic/image-20200615225523090.png)



# 

