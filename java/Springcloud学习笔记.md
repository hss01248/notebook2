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

#### 降级

![image-20210710113124578](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710113124578.jpg)

![image-20210706223230656](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210706223230656.png.png)

![image-20210706222205127](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210706222205127.png.png)



![image-20210706223650316](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210706223650316.png.png)



![image-20210706223801138](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210706223801138.png.png)

![image-20210706223347706](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210706223347706.png.png)

![image-20210706223720526](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210706223720526.png.png)

![image-20210706224209375](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210706224209375.png.png)

##### 全局降级

###### 在controller上统一声明降级

![image-20210710111652967](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710111652967.jpg)

![image-20210710111823302](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710111823302.jpg)

![image-20210710111923502](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710111923502.jpg)

![image-20210710111954089](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710111954089.jpg)



###### 更进一步,在调用的服务接口上声明降级:

![image-20210710112507623](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710112507623.jpg)

 

![image-20210710112712773](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710112712773.jpg)

![image-20210710112648947](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710112648947.jpg)

![image-20210710112745333](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710112745333.jpg)

![image-20210710112820706](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710112820706.jpg)

##### 熔断

![image-20210710113207801](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710113207801.jpg)

![image-20210710113300347](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710113300347.jpg)

 

![image-20210710113407580](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710113407580.jpg)

![image-20210710113500767](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710113500767.jpg)

![image-20210710121239215](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710121239215.jpg)

![image-20210710121348723](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710121348723.jpg)

![image-20210710171342348](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710171342348.jpg)

![image-20210710171652386](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710171652386.jpg)

![image-20210710172133925](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710172133925.jpg)

![image-20210710172150741](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710172150741.jpg)

![image-20210710172910361](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710172910361.jpg)

断路器打开后:

![image-20210710173005370](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710173005370.jpg)

常用value

![image-20210710173041086](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710173041086.jpg)

##### 限流 -Alibaba sentinel时讲



##### hytix总结

工作流程图

![image-20210710173243882](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710173243882.jpg)

![image-20210710174839517](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710174839517.jpg)

##### dashboard

![image-20210710175017955](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710175017955.jpg)

![image-20210710175028820](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710175028820.jpg)

![image-20210710175235547](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710175235547.jpg)

![image-20210710175259938](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710175259938.jpg)

![image-20210710175341268](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710175341268.jpg)

 

![image-20210710175506436](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710175506436.jpg)

![image-20210710175528574](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710175528574.jpg)

![image-20210710175720401](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710175720401.jpg)



![image-20210710175623598](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710175623598.jpg)

![image-20210710175942281](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710175942281.jpg)

![image-20210710175815507](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710175815507.jpg)

![image-20210710175900365](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710175900365.jpg)

![image-20210710180017697](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710180017697.jpg)

![image-20210710180041255](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710180041255.jpg)

![image-20210710180056340](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710180056340.jpg)

![image-20210710180112598](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710180112598.jpg)



![image-20210710180132361](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710180132361.jpg)

### 2.4 服务网关

![image-20210710180310928](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710180310928.jpg)

 

#### 2.4.1 zuul

![image-20210710184314906](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710184314906.jpg)

#### 2.4.2 spring cloud gateway

![image-20210710184537331](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710184537331.jpg)

![image-20210710184954997](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710184954997.jpg)

![image-20210710185352672](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710185352672.jpg)

![image-20210710185101967](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710185101967.jpg)



![image-20210710185052605](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710185052605.jpg)

![image-20210710185117380](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710185117380.jpg)

![image-20210710185131930](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710185131930.jpg)

netty视频: 韩树平

https://www.bilibili.com/video/BV1DJ411m7NR

 ![image-20210710185406759](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710185406759.jpg)

![image-20210710185425753](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710185425753.jpg)

##### 特性

![image-20210710185556843](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710185556843.jpg)

##### 与zuul的区别 

![image-20210710185634621](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710185634621.jpg)

###### zuul模型

![image-20210710185805443](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710185805443.jpg)

![image-20210710185827224](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710185827224.jpg)

###### gateway-非阻塞

![image-20210710185937095](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710185937095.jpg)

![image-20210710185958952](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710185958952.jpg)

##### 核心概念

![image-20210710190109078](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710190109078.jpg)

 ![image-20210711091539803](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711091539803.png.jpg)

![image-20210710190145421](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710190145421.jpg)

![image-20210710190325194](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710190325194.jpg)

![image-20210710190437598](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710190437598.jpg)

![image-20210710190514002](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710190514002.jpg)

![image-20210710190526636](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710190526636.jpg)

##### 配置

![image-20210710190625966](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710190625966.jpg)

![image-20210710190654221](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710190654221.jpg)

![image-20210710201644544](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710201644544.jpg)

![image-20210710201608829](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710201608829.jpg)

![image-20210710190827117](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710190827117.jpg)

![image-20210710190838052](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710190838052.jpg)

![image-20210710190914474](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710190914474.jpg)

![image-20210710191005702](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710191005702.jpg)

![image-20210710201745392](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710201745392.jpg)

![image-20210710201803558](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710201803558.jpg)

![image-20210710201856156](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710201856156.jpg)

###### 代码配置方式

![image-20210710205409912](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710205409912.png.jpg)

![image-20210710205437698](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710205437698.png.jpg)

![image-20210710205621627](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710205621627.png.jpg)

![image-20210710205725280](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710205725280.png.jpg)

![image-20210710210015611](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210710210015611.png.jpg)

##### 由网关实现负载均衡



##### predicate

![image-20210711090351015](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711090351015.png.jpg)

![image-20210711090406833](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711090406833.png.jpg)

![image-20210711090425587](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711090425587.png.jpg)

![image-20210711090451884](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711090451884.png.jpg)

![image-20210711091221863](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711091221863.png.jpg)

![image-20210711091250423](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711091250423.png.jpg)

![image-20210711091342722](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711091342722.png.jpg)

![image-20210711091410813](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711091410813.png.jpg)

![image-20210711091440694](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711091440694.png.jpg)

##### filter

![image-20210711091623543](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711091623543.png.jpg)

![image-20210711091637464](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711091637464.png.jpg)

###### 单一的

![image-20210711091827584](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711091827584.png.jpg)

![xxxx](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711091722535.png.jpg.jpg.jpg)

![image-20210711092013311](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711092013311.png.jpg)



###### 全局的:

![image-20210711091759544](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711091759544.png.jpg)



###### 自定义过滤器

![image-20210711092042544](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711092042544.png.jpg)

![image-20210711092402524](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711092402524.png.jpg)

### 2.5 配置中心



#### 2.5.1 概述

![image-20210711092917249](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711092917249.png.jpg)

![image-20210711093342770](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711093342770.png.jpg)

![image-20210711095834364](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711095834364.png.jpg)



![image-20210711095911121](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711095911121.png.jpg)

![image-20210711100218314](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711100218314.png.jpg)

![image-20210711100245323](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711100245323.png.jpg)

![image-20210711100318373](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711100318373.png.jpg)

![image-20210711100348773](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711100348773.png.jpg)

#### 服务端

![image-20210711100530004](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711100530004.png.jpg)

![image-20210711104204315](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711104204315.png.jpg)

![image-20210711104147067](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711104147067.png.jpg)

![image-20210711104242956](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711104242956.png.jpg)

![image-20210711104319529](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711104319529.png.jpg)

![image-20210711104414176](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711104414176.png.jpg)

![image-20210711104441258](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711104441258.png.jpg)

<img src="https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711104725660.png.jpg" alt="image-20210711104725660" style="zoom:150%;" />

![image-20210711104747406](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711104747406.png.jpg)

![image-20210711104849447](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711104849447.png.jpg)





![image-20210711105148845](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711105148845.png.jpg)

![image-20210711105212604](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711105212604.png.jpg)



![image-20210711105318425](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711105318425.png.jpg)

![image-20210711105258254](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711105258254.png.jpg)

![image-20210711105344008](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711105344008.png.jpg)

#### 客户端

![image-20210711105535167](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711105535167.png.jpg)

![image-20210711105631809](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711105631809.png.jpg)

![image-20210711105831448](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711105831448.png.jpg)

![image-20210711105930000](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711105930000.png.jpg)

![image-20210711110135502](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711110135502.png.jpg)

![image-20210711110115077](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711110115077.png.jpg)

#### 配置的动态刷新问题

![image-20210711110415562](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711110415562.png.jpg)

![image-20210711111440105](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711111440105.png.jpg)



![image-20210711111501823](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711111501823.png.jpg)

![image-20210711111350116](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711111350116.png.jpg)

![image-20210711111533964](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711111533964.png.jpg)

![image-20210711111745742](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711111745742.png.jpg)

![image-20210711111808680](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711111808680.png.jpg)

![image-20210711112011804](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711112011804.png.jpg)

### 2.6 消息总线Bus

![image-20210711112247575](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711112247575.png.jpg)

![image-20210711112306345](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711112306345.png.jpg)

![image-20210711112321695](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711112321695.png.jpg)

![image-20210711112346183](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711112346183.png.jpg)

![image-20210711112704458](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711112704458.png.jpg)

![image-20210711112731090](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711112731090.png.jpg)

![image-20210711112904557](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711112904557.png.jpg)

![image-20210711112924890](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711112924890.png.jpg)

![image-20210711112941176](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711112941176.png.jpg)

![image-20210711112956713](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711112956713.png.jpg)

![image-20210711113014409](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711113014409.png.jpg)

guest  guest

![image-20210711113053194](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711113053194.png.jpg)

#### 使用bus广播配置中心的更新

![image-20210711113218208](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711113218208.png.jpg)

![image-20210711113413534](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711113413534.png.jpg)

![image-20210711113458056](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711113458056.png.jpg)

![image-20210711113514390](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711113514390.png.jpg)

![image-20210711113539219](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711113539219.png.jpg)

##### 配置 配置中心

![image-20210711113713630](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210711113713630.png)

![image-20210711113754116](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711113754116.png.jpg)

![image-20210711113816055](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711113816055.png.jpg)

##### 配置客户端

![image-20210711113945972](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711113945972.png.jpg)

![image-20210711114000600](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711114000600.png.jpg)

![image-20210711114117466](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711114117466.png.jpg)

![image-20210711114207864](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711114207864.png.jpg)

#### 定点通知

![image-20210711114429256](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711114429256.png.jpg)

![image-20210711114455747](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711114455747.png.jpg)

![image-20210711114530929](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711114530929.png.jpg)

#### 总结

![image-20210711114651784](https://gitee.com/hss012489/picbed/raw/master/picgo/image-20210711114651784.png.jpg)

### 2.7 stream 消息驱动

