### composer 使用流程

#### 安装方式
* HomeBrew
```bash
brew install composer
```
* 手动下载
```bash
wget https://getcomposer.org/composer.phar
mv composer.phar /usr/local/bin/composer
chmod +x /usr/local/bin/composer
```
* 官方推荐安装方式
```bash
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('SHA384', 'composer-setup.php') === '669656bab3166a7aff8a7506b8cb2d1c292f042046c5a994c43155c0be6190fa0355160742ab2e1c88d40d5be660b410') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
ln -s /opt/composer.phar /usr/bin/composer
```

#### composer 版本号的组成
MAJOR.MINOR.PATCH

MAJOR(主版本号): 通常会发生 API 变更，不向后兼容
MINOR(次版本号): 新增功能，但是向后兼容
PATCH(修订版本号): 补丁，向后兼容，修复 bug
附加标识： 在版本号后面可能会有附加标识，例如预发布版本（例如dev、alpha、beta、RC、stable等）或构建元数据（例如构建号）。这些标识是可选的，并且用于表示版本的开发状态或其他额外信息。

### 安装依赖包
#### 方式一：
编辑 composer.json && composer update
#### 方式二：
```bash
composer require overtrue/wechat
composer require overtrue/wechat:~3.1
```      

#### 版本号的各种写法
* 确切版本： "1.0.2"
* 范围： ">=1.0", ">=1.0 <2.0", ">=1.0 <1.1 || >=1.2"
* 连字符范围： "1.0 - 2.0"
* 通配符： "1.0.*"
* ~运算符： "~1.2.3"
* ^运算符： "^1.2.3"

#### 版本锁定
* 版本锁定文件： composer.lock
* composer install 产生它 & 使用它
* composer update 更新它
* composer.lock 是否应该加入版本控制？ Project 需要，Library 不需要

#### 发布公司内部用的闭源包
* 公司内部用的包，上传到 GitLab 或者其它私有的代码托管平台。
* 在composer.json文件中添加repositories直接用vcs指定代码库地址

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


