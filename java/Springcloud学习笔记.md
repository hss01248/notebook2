# spring cloud 学习笔记



# 1 spring boot的一些补课

## 数据库druid

控制台url  http://localhost:8001/druid

## 日志

门面+ 实现.

为了方便,直接使用hutool的StaticLog.xxx api



## 热部署 devtools配置

![image-20210703202215985](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210703202215985.png.png)





# 2 springcloud

## 2.1 注册中心

### 2.1.1 eureka

> 已停止维护

http://localhost:7001/



单机模拟集群配置:

![image-20210704103451762](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704103451762.png.png)



### 2.1.2 zookepper

> 用得少

![image-20210704131739862](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704131739862.png.png)

![image-20210704131659381](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704131659381.png.png)

![image-20210704131803593](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704131803593.png.png)

![image-20210704131844808](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704131844808.png.png)

为临时节点

![image-20210704132147936](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704132147936.png.png)

### 2.1.3 consul

![image-20210704133340173](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704133340173.png.png)

![image-20210704133425210](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704133425210.png.png)



![image-20210704180245664](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704180245664.png.png)



### 比较

![image-20210704191606659](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704191606659.png.png)



![image-20210704191659663](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704191659663.png.png)

![image-20210704191728902](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704191728902.png.png)

![image-20210704192100710](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704192100710.png.png)

![image-20210704192237792](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704192237792.png.png)

## 2.2 http/IPC调用+负载均衡

![image-20210704194404731](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704194404731.png.png)

### 负载均衡

![image-20210704194739777](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704194739777.png.png)

![image-20210704194523263](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704194523263.png.png)

![image-20210704194634988](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704194634988.png.png)

![image-20210704194713598](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704194713598.png.png)







### 2.2.1 ribbon

![image-20210704194848424](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704194848424.png.png)

> 不再更新

![image-20210704194202224](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704194202224.png.png)

![image-20210704194331713](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704194331713.png.png)

![image-20210704194442035](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704194442035.png.png)



![image-20210704200652873](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704200652873.png.png)

![image-20210704200729820](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704200729820.png.png)



eureka client自带ribbon

![image-20210704200843787](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704200843787.png.png)

![image-20210704200942109](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704200942109.png.png)



#### 路由规则

![image-20210704210337298](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704210337298.png.png)



![image-20210704210412315](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704210412315.png.png)



![image-20210704210546274](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704210546274.png.png)



![image-20210704210620788](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704210620788.png.png)



![image-20210704210725952](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704210725952.png.png)



#### 负载均衡原理

![image-20210704211722771](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704211722771.png.png)

##### 轮询算法:

![image-20210704212059957](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704212059957.png.png)

![image-20210704212542037](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704212542037.png.png)

![image-20210704212736410](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704212736410.png.png)

自旋锁

![image-20210704212824617](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704212824617.png.png)



##### 自己实现负载均衡

![image-20210704214058010](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704214058010.png.png)

![image-20210704214141918](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704214141918.png.png)



![image-20210704214623856](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704214623856.png.png)

![image-20210704214835595](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704214835595.png.png)

![image-20210704215053405](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704215053405.png.png)



#### restTemplate

![image-20210704201144171](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704201144171.png.png)

![image-20210704201219578](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210704201219578.png.png)

#### 2.2.2 openFeign

![image-20210705071111229](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210705071111229.png.png)

![image-20210705071152770](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210705071152770.png.png)

![image-20210705071259694](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210705071259694.png.png)



![image-20210705072242678](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210705072242678.png.png)

![image-20210705072324231](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210705072324231.png.png)

![image-20210705072446543](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210705072446543.png.png)

![image-20210705073000704](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210705073000704.png.png)

![image-20210705073656789](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210705073656789.png.png)

![image-20210705073801397](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210705073801397.png.png)

日志级别

![image-20210705073844099](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210705073844099.png.png)

![image-20210705073911041](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210705073911041.png.png)

![image-20210705074010012](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210705074010012.png.png)



### 2.3 服务熔断,降级,限流

#### 2.3..1 Hytrix

![image-20210706223230656](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210706223230656.png.png)

![image-20210706222205127](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210706222205127.png.png)



![image-20210706223650316](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210706223650316.png.png)



![image-20210706223801138](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210706223801138.png.png)

![image-20210706223347706](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210706223347706.png.png)

![image-20210706223720526](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210706223720526.png.png)

![image-20210706224209375](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210706224209375.png.png)













