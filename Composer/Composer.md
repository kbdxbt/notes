### composer 使用流程

#### 流程一 新项目流程
* 创建 composer.json，并添加依赖到的扩展包；
* 运行 composer install，安装扩展包并生成 composer.lock；
* 提交 composer.lock 到代码版本控制器中，如：git;
#### 流程二 项目协作者安装现有项目
*  克隆项目后，根目录下直接运行 composer install 从 composer.lock 中安装 指定版本 的扩展包以及其依赖；
#### 流程三：为项目添加新扩展包
* 使用 composer require vendor/package 添加扩展包；
* 提交更新后的 composer.json 和 composer.lock 到代码版本控制器中，如：git;
#### 流程四 线上部署新项目
*  克隆项目后，根目录下直接运行 composer install --no-dev；

ps : 原有项目新添加扩展的，都使用 composer require new/package 这种方式来安装。需要加版本的话，
```bash
composer require "foo/bar:1.0.0"
composer require "foo/bar:1.0.0" --dev
```
