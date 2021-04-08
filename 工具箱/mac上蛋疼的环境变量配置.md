# mac上蛋疼的环境变量配置

bash是什么?zsh又是什么?oh-my-zsh又是什么?

为什么在.bash_profile里配置了不生效?

为什么使用了source .bash_profile命令还是不生效?

怎么查看真正生效的path?

把所有带zsh的文件夹都删了,怎么办?



各种懵逼,你跟windows一样做个图形界面不好么?mac这傻叉的设计



![image-20210408105119273](https://gitee.com/hss012489/picbed/raw/master/picgo/1617850285885-image-20210408105119273.jpg)

另外还有.zshrc



去对应文件看看到底是什么鬼:

/etc/profile:

![image-20210408105303833](https://gitee.com/hss012489/picbed/raw/master/picgo/1617850383871-image-20210408105303833.jpg)

/etc/paths:

![image-20210408105339832](https://gitee.com/hss012489/picbed/raw/master/picgo/1617850419861-image-20210408105339832.jpg)

.bash_profile:

![image-20210408105424831](https://gitee.com/hss012489/picbed/raw/master/picgo/1617850464861-image-20210408105424831.jpg)

## 首先,怎么查看真正生效的path:

一个个敲命令验证?太傻了,直接上代码看:

```java
            Map map = System.getenv();
            Iterator it = map.entrySet().iterator();
            while(it.hasNext())
            {
                Map.Entry entry = (Map.Entry)it.next();
                System.out.print(entry.getKey()+"=");
                System.out.println(entry.getValue());
            }
```

输出:

```java
PATH=/usr/bin:/bin:/usr/sbin:/sbin
SHELL=/bin/bash
  ...
```

可以看到,只是/etc/paths生效了,上面的.bash_profile并没有生效.为啥?

因为格式不对: 应该是如下的格式:

![image-20210408105912038](https://gitee.com/hss012489/picbed/raw/master/picgo/1617850752066-image-20210408105912038.jpg)

> **先定义路径`（MAVEN_HOME）`，后用path引入`（PATH）`，是从上到下的顺序，要不然就读不出。 另外一点就是在`bash_profile`图中我最后添加了`$PATH:`这里通过它引用了**/etc/paths的命令



上面改写成下面,然后source .bash_profile使之立刻生效. 

![image-20210408110558488](https://gitee.com/hss012489/picbed/raw/master/picgo/1617851158520-image-20210408110558488.jpg)

可以看到path变成了:

![image-20210408110730392](https://gitee.com/hss012489/picbed/raw/master/picgo/1617851250421-image-20210408110730392.jpg)



终于,使用npm,adb等命令都没有问题了.