# 最新图像压缩格式 AVIF



# 官方

文档: https://aomediacodec.github.io/av1-avif/

源码: https://github.com/AOMediaCodec/libavif

格式登记: https://www.iana.org/assignments/media-types/image/avif

google blog: https://jakearchibald.com/2020/avif-has-landed/





# 相关介绍

[AVIF图片格式简介](https://www.zhangxinxu.com/wordpress/2020/04/avif-image-format/)

[未来取代 JPEG 的，可能是 Android 12 支持的新图片格式](https://tech.ifeng.com/c/84IJDmNM91u)

#  描述

HEIF因为专利问题已被时代抛弃

webp推广不力

jpg老大地位一直坚挺

avif基于最新视频压缩算法,压缩比超高,且开源免费,最有希望能冲击jpg的地位. 几个互联网巨头都下注.

# 效果比对:

https://www.dogedoge.com/image_formats/



![image-20210305095437583](https://gitee.com/hss012489/picbed/raw/master/picgo/1614909284128-image-20210305095437583.jpg)

n次重复压缩对比:

![image-20210305100137899](https://gitee.com/hss012489/picbed/raw/master/picgo/1614909697928-image-20210305100137899.jpg)



# 编解码的源码(C)

https://github.com/AOMediaCodec/libavif

上面的效果比对的思路是: 一张高清大图用avif格式压制后,看文件大小.

然后原图用jpeg压缩到同等大小,对比效果.



## 压缩质量的说明:

**Change the AVIF quality setting**
Go-avif uses the libaom quality setting (called “Q mode”), which starts at 0 (lossless) and goes up to 63 (worst quality). If not specified, go-avif sets the compression quality to 25. Here is an example of AVIF encoding with a slightly better quality:

On Windows:
`avif-win-x64.exe -e example.JPG -o example-q20.avif -q 20`



各平台的一些工具:

https://libre-software.net/avif-test/

# web端代码库

[avif.js](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2FKagami%2Favif.js)

[使用 AVIF 图片，优化前端项目](https://www.jianshu.com/p/e89971aaf52a)

# Android端代码库

https://github.com/link-u/AndroidGlideAvifDecoder

https://github.com/programmingisart/avif_viewer_android



# 压缩工具和代码

https://github.com/programmingisart/avif_viewer_android

将avif文件转换成高质量的jpg文件,然后用Android Bitmap api加载



# 对比demo

https://github.com/hss01248/avif-android

### 效果图

单反拍摄的图,放大到100%后对比:

![Screenshot_20210401_102002_com.jackco.avifencoder(1)](https://gitee.com/hss012489/picbed/raw/master/picgo/1617261222926-Screenshot_20210401_102002_com.jackco.avifencoder(1).jpg)

### avif的额外功效: 

磨平jpeg压缩产生的不合理色块

下图avif是以质量80的jpg图为源图来进行压缩的.

可以看到源图上有色块,avif上已经没有了

![image-20210401154837974](https://gitee.com/hss012489/picbed/raw/master/picgo/1617263318011-image-20210401154837974.jpg)

# 文件格式分析

avif与heif文件格式保持兼容. 

下面是heif的格式:

[HEIF/heic图片文件解析](https://zhuanlan.zhihu.com/p/35847861)

![img](https://gitee.com/hss012489/picbed/raw/master/picgo/1617344063552-v2-1221190c851c7c6b0d220a0c76bd6db3_720w.jpg)

看avif:

![image-20210402141616696](https://gitee.com/hss012489/picbed/raw/master/picgo/1617344176735-image-20210402141616696.jpg)

### 文件头:

![image-20210401144211574](https://gitee.com/hss012489/picbed/raw/master/picgo/1617259336745-image-20210401144211574.jpg)

![image-20210401144239633](https://gitee.com/hss012489/picbed/raw/master/picgo/1617259359660-image-20210401144239633.jpg)



看起来,文件格式写在前8个字节,ftypavif

exif信息已预留保存区:mata开始到数据区结束

数据区开始标识: mdat->6D 64 61 74 12 00 0A

![image-20210401145635280](https://gitee.com/hss012489/picbed/raw/master/picgo/1617260195310-image-20210401145635280.jpg)

再看一个经过exiftool拷贝exif的图:

```shell
/usr/local/bin/exiftool -TagsFromFile /Users/hss/Downloads/IMG_20210301_173148441617248922924.jpg /Users/hss/Downloads/IMG_20210301_17314844-q1-25-q2-36.avif
```

![image-20210401145810837](https://gitee.com/hss012489/picbed/raw/master/picgo/1617260290864-image-20210401145810837.jpg)



## 文件尾:

并无特征. 没有像jpg那样的固定ffd9的结束标识.

![image-20210401144929339](https://gitee.com/hss012489/picbed/raw/master/picgo/1617259769371-image-20210401144929339.jpg)

![image-20210401144948933](https://gitee.com/hss012489/picbed/raw/master/picgo/1617259788960-image-20210401144948933.jpg)



# 待解决问题

* 蓝绿色偏色问题: 根据亮度推导色度,算法本身缺陷?
* exif读写问题:  how to write tiff format exif data into a avif file?

偏色情况: 蓝绿变亮,黄色变暗:

![image-20210401155923303](https://gitee.com/hss012489/picbed/raw/master/picgo/1617263963342-image-20210401155923303.jpg)

# oritation的处理

https://zpl.fi/exif-orientation-in-different-formats/

# 探究:exif拷贝: 直接从jpeg拷贝一份exif信息到avif中

```
//meta:  6d 65 74 64
//mdat : 6d 64 61 74
删除区间内容,发现图片无法打开.
```

[Exif文件格式描述](https://blog.csdn.net/huang546213693/article/details/46503361)

exif查看: https://exif.tuchong.com/

![image-20210402150500236](https://gitee.com/hss012489/picbed/raw/master/picgo/1617347100277-image-20210402150500236.jpg)



## 直接看源码是怎么写exif的:

https://github.com/AOMediaCodec/libavif/blob/master/src/write.c

![image-20210402165504505](https://gitee.com/hss012489/picbed/raw/master/picgo/1617353704566-image-20210402165504505.jpg)