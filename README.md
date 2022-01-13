# Composer踩坑记录

Composer是PHP语言用来解决包依赖问题的包管理工具，众所周知的原因https://packagist.org/在国内是打不开的，但是国内能找到镜像源，使用起来还是遇到一些问题，下面我对遇到的问题进行记录。

## 坑
1. 设置了镜像`composer config -g repos.packagist composer https://mirrors.aliyun.com/composer/`，使用`composer search phpunit`时还是使用
