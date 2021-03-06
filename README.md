# Composer踩坑记录

## 笔记

* Composer是PHP的依赖管理工具，不是包管理器。
* 声明依赖关系你需要在项目中创建`composer.json`文件，其中描述项目的依赖关系
```json
{
    "require": {
        "monolog/monolog": "1.2.*"
    }
}
```
* 运行Composer需要PHP 5.3.2+以上版本
* Composer从包的来源直接安装，所以需要git、svn或者hg
* Composer支持Windows、Linux和OSX
* 执行`php composer.phar install`或者`composer install`安装依赖
* Composer安装依赖后会生成`composer.lock`文件
* 例如安装monolog，会安装到`vendor/monolog/monolog`目录
* Composer安装依赖后会生成自动加载文件，位置在`vendor/autoload.php`，把它引入项目的引导文件中可以使用Composer安装的所有库的类文件
* 你可以在 composer.json 的 autoload 字段中增加自己的 autoloader
  ```json
  {
      "autoload": {
          "psr-4": {"Acme\\": "src/"}
      }
  }
  ```
  * Composer 将注册一个 PSR-4 autoloader 到 Acme 命名空间
  * 此时 src 会在你项目的根目录，与 vendor 文件夹同级。例如 src/Foo.php 文件应该包含 Acme\Foo 类
  * 添加 autoload 字段后，你应该再次运行 install 命令来生成 vendor/autoload.php 文件
  * 引用这个文件也将返回 autoloader 的实例，你可以将包含调用的返回值存储在变量中，并添加更多的命名空间。这对于在一个测试套件中自动加载类文件是非常有用的，例如
  ```php
  $loader = require 'vendor/autoload.php';
  $loader->add('Acme\\Test\\', __DIR__);
  ```
  * 除了 PSR-4 自动加载，classmap 也是支持的。这允许类被自动加载，即使不符合 PSR-0 规范
  * Composer 提供了自己的 autoloader。如果你不想使用它，你可以仅仅引入`vendor/composer/autoload_*.php`文件，它返回一个关联数组，你可以通过这个关联数组配置自己的`autoloader`
* 包版本
  * 版本约束可以用几个不同的方法来指定
    * 确切版本号 `1.0.2`
    * 范围 `>=1.0` `>=1.0,<2.0` `>=1.0,<1.1|>=1.2`
      * 通过使用比较操作符可以指定有效的版本范围
      * 有效的运算符：>、>=、<、<=、!=
      * 你可以定义多个范围，用逗号隔开，这将被视为一个逻辑AND处理。一个管道符号|将作为逻辑OR处理。AND 的优先级高于 OR
    * 通配符 `1.0.*`
      * 你可以使用通配符*来指定一种模式。1.0.*与>=1.0,<1.1是等效的
    * 赋值运算符 `~1.2`
      * 这对于遵循语义化版本号的项目非常有用。~1.2相当于>=1.2,<2.0
      * ~ 最好用例子来解释： ~1.2 相当于 >=1.2,<2.0，而 ~1.2.3 相当于 >=1.2.3,<1.3。正如你所看到的这对于遵循 语义化版本号 的项目最有用。一个常见的用法是标记你所依赖的最低版本，像 ~1.2 （允许1.2以上的任何版本，但不包括2.0）。由于理论上直到2.0应该都没有向后兼容性问题，所以效果很好。你还会看到它的另一种用法，使用 ~ 指定最低版本，但允许版本号的最后一位数字上升。
* `composer.lock`和`composer.json`提交到版本库中
* 依赖的库更新了版本，需要用`update`命令更新依赖
* 只更新一个依赖`php composer.phar update monolog/monolog`
* packagist是Composer的资源库
* 只要你有一个 composer.json 文件在目录中，那么整个目录就是一个包
* 项目和库之间唯一的区别是，项目是一个没有名字的包
* 为了使它成为一个可安装的包，你需要给它一个名称。你可以通过 composer.json 中的 name 来定义
```json
{
    "name": "acme/hello-world",
    "require": {
        "monolog/monolog": "1.0.*"
    }
}
  * 在这种情况下项目的名称为 acme/hello-world，其中 acme 是供应商的名称。供应商的名称是必须填写的
```
* `composer show --platform` 命令来获取可用的平台软件包的列表

## 问题记录

Composer是PHP语言用来解决包依赖问题的包管理工具，众所周知的原因[https://packagist.org/](https://packagist.org/)在国内是打不开的，但是国内能找到镜像源，使用起来还是遇到一些问题，下面我对遇到的问题进行记录。

### 坑
* 设置了镜像`composer config -g repos.packagist composer https://mirrors.aliyun.com/composer/`，使用`composer search phpunit`时还是使用
