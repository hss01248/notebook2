# so动态加载



# 一些参考

https://segmentfault.com/a/1190000038995672

http://www.duqian.site/2019/05/07/The%20Best%20SoLoader-%E4%BC%98%E9%9B%85%E7%9A%84so%E5%8A%A8%E6%80%81%E5%8A%A0%E8%BD%BD%E6%96%B9%E6%A1%88/

https://github.com/duqian291902259/DQ-Android-Labs/tree/master/soloader/src/main/java/site/duqian/soloader

https://github.com/niezhiyang/StudyDemo/tree/master/DemoSo

https://cloud.tencent.com/developer/article/1643819



# 试用:

https://github.com/skyNet2017/opencv_wechat_qr



# 整体思路:

放于github,使用jsdeliver加速,必要时使用token鉴权

使用filedownloader下载,校验md5

注入paths

使用relinker加载,回调成功和失败

