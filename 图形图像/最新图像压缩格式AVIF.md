# 最新图像压缩格式 AVIF

# 相关介绍

[AVIF图片格式简介](https://www.zhangxinxu.com/wordpress/2020/04/avif-image-format/)

[未来取代 JPEG 的，可能是 Android 12 支持的新图片格式](https://tech.ifeng.com/c/84IJDmNM91u)

# # 描述

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

目前不支持质量设置. 

上面的效果比对的思路是: 一张高清大图用avif格式压制后,看文件大小.

然后原图用jpeg压缩到同等大小,对比效果.



# web端代码库

[avif.js](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2FKagami%2Favif.js)

[使用 AVIF 图片，优化前端项目](https://www.jianshu.com/p/e89971aaf52a)

# Android端代码库

https://github.com/link-u/AndroidGlideAvifDecoder

https://github.com/programmingisart/avif_viewer_android



# 压缩工具和代码

https://github.com/programmingisart/avif_viewer_android

将avif文件转换成高质量的jpg文件,然后用Android Bitmap api加载