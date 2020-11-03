# git

# rebase操作

> 在Android studio或任意jetbrains的IDE上

## 最佳实践场景: 

分支开发

本地多次提交

合并代码到release分支时,

使用rebase将本地的此需求相关提交的commit全部合并为一个commit,再提交到远程.

然后再与release分支进行交互.

### 注意事项:

如果一些commit预先提交到了远程,再rebase本地,再次push时会提示代码冲突.

# 操作图

意图合并此五个commit为一个commit:

![image-20201103164140427](https://gitee.com/hss012489/picbed/raw/master/picgo/1604392910476-image-20201103164140427.jpg)

选择时间最前的commit,鼠标右键点击:

![image-20201103164322372](https://gitee.com/hss012489/picbed/raw/master/picgo/1604393004478-image-20201103164322372.jpg)

![image-20201103164444048](https://gitee.com/hss012489/picbed/raw/master/picgo/1604393085646-image-20201103164444048.jpg)

后续每一项依次选中一下,点击一下2处的按钮

直到所有都加入了线里.

最后点击开始

![image-20201103164701336](https://gitee.com/hss012489/picbed/raw/master/picgo/1604393223346-image-20201103164701336.jpg)

![image-20201103164809789](https://gitee.com/hss012489/picbed/raw/master/picgo/1604393291459-image-20201103164809789.jpg)

效果:

![image-20201103164937857](https://gitee.com/hss012489/picbed/raw/master/picgo/1604393379559-image-20201103164937857.jpg)