### 常用命令
- 镜像
```
# 配置中国镜像
composer config -g repo.packagist composer https://packagist.phpcomposer.com
# 配置其他厂商镜像
# 阿里云 (好像说停用了)
composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/
# 腾讯
composer config -g repo.packagist composer https://mirrors.cloud.tencent.com/composer/
# 华为
composer config -g repo.packagist composer https://repo.huaweicloud.com/repository/php/
# 解除镜像
composer config -g --unset repos.packagist
```

- 项目
```
# 搜索项目
composer search ThinkPHP
# 创建项目
composer create-project topthink/ThinkPHP=5.1.* ./tp5
# 安装扩展
composer require laravel/laravel ">=5.5"
# 移除扩展
composer remove laravel/laravel
```

- 生产最佳实践
```
# 转换 PSR-0/4 autoloading 到 classmap 获得更快的载入速度,禁用开发者模式
composer dump-autoload -o --no-dev 
```

### 自动加载 autoload
目前支持四种自动加载方式:
- PSR-0
  PSR-0规范是PHP5.2之前的一种命名空间映射规范，它规定命名空间与文件路径的对应关系如下
    1. 命名空间中的每个下划线字符（_）都会被转换成目录分隔符（/）；
    2. 命名空间中的每个命名空间分隔符（\）都会被转换成目录分隔符（/）；
    3. 命名空间中的首字母和下划线字符都会被转换成目录名和文件名中的小写字母；
    4. 每个类的文件名必须与类名完全一致，包括大小写。
  ```
  "psr-0" : {
      "Foo\\" : "psr0src/",
      # 
      "Foo_Bar_" : "psr0src/"
  },
  ```
  composer install/update之后,PSR-0引用全部合并到vendor/composer/autoload_namespaces.php

- PSR-4
  PSR-4规范是较新的一种命名空间映射规范，它与PSR-0规范的区别在于：
    1. 命名空间中的下划线字符不再被特殊处理，只有命名空间分隔符（\）会被转换成目录分隔符（/）；
    2. 命名空间中的首字母和下划线字符不再被强制转换成小写字母；
    3. 类的文件名与类名可以不完全一致，但必须满足相对文件路径和类名的对应关系
```
"psr-4": {
	#查找Afishpapa\Httptool\Http类时的路径为src/Http.php
	"Afishpapa\\Httptool\\": "src/"	
	#可以在src/和lib/ 下面找 Monolog命名空间下的类
	"psr-4": { "Monolog\\": ["src/", "lib/"] }
	#所有命名空间都来src/目录下找
	"" : "src/"
}
```
composer install/update之后,PSR-4引用全部合并到vendor/composer/automoad_psr4.php中。
```
return array(
    'Afishpapa\\Httptool\\' => array($baseDir . '/src'),
);
```

- Classmap
  composer install/update之后,PSR-4引用全部合并到vendor/composer/autoload_classmap.php中。
  你可以用 classmap 生成支持支持自定义加载的不遵循 PSR-0/4 规范的类库。


- Files
  通常作为函数库的载入方式（而非类库）。
```
{
    "autoload": {
        "files": ["src/MyLibrary/functions.php"]
    }
}
```



### 全局配置
```
-v : 增加消息的详细程度,正常输出
-vv : 增加消息的详细程度,更加详细得输出
-vvv : 增加消息的详细程度,debug用
-h : 显示帮助
-q : 不要显示任何信息
-n : 不要问任何交互问题
-d : 设置工作目录
--ansi: 强制输出 ANSI 编码
--no-ansi: 禁用 ANSI 编码
--version (-V): 展示所有应用版本
--profile: 展示时间和内存信息
```

### 常用配置
```
--prefer-install: 默认值为dist
	dist: 检查本地缓存压缩包,如果有直接复制到vendor目录,如果本地缓存没有,则去远程仓库下载压缩包,如果远程仓库没有提供压缩包,则尝试从github中安装包,并且删除.git版本信息,总之,dist能够快速地下载并安装依赖包，适用于大部分生产环境。
	source: 直接从github中下载源码,保留.git信息,如果您需要对包进行自定义修改或者需要对其进行特殊的构建过程，则应该使用 source。
	auto:2.1版本后已弃用
	
-o: 生成自动加载器文件的优化版本，以加快类加载速度。

-a: 默认情况下，Composer 会根据 composer.json 中的 PSR-4 和 PSR-0 配置来生成自动加载器, 使用 --classmap-authoritative 选项可以让 Composer 忽略 PSR-4 和 PSR-0 配置，而直接根据类文件生成一个类映射表（class map），并将其作为自动加载器的唯一来源。需要注意的是，使用 --classmap-authoritative 选项可能会导致一些问题，比如在添加新的类文件时需要重新生成类映射表，否则新添加的类无法被自动加载器加载。因此，建议在开发环境中使用这个选项来提高性能，但在生产环境中不要使用，以免出现问题。

--dry-run: 执行安装过程的模拟运行，不会实际下载或安装任何软件包。

--dev: 安装开发依赖项，包括测试框架和调试工具等。
```

### composer init
以交互方式初始化composer
```
--name: 包名,格式为作者/名称,比如monolog/monolog
--description: 简短描述
--author: 作者名
--type: 包的安装类型,默认library
	library : 它会简单的将文件复制到 vendor 目录
	project : 当前包是一个项目，而不是一个库
	metapackage : 一个空的包，包含依赖并且需要触发依赖的安装，这将不会对系统写入额外的文件。
	composer-plugin : 它有一个自定义安装类型，可以为其它包提供一个 installler。
--homepage: 官网首页
--require: 引入包,格式为 包:版本 foo/bar:1.0.0
--require-dev: 开发用的组件
-s : 最小稳定性值 dev stable 
--license (-l): 许可证
--repository: 指定一个或多个 Composer 仓库
-a : 添加一个autoload.psr-4的对象到composer.json
```

### composer install
如果存在 `composer.lock` 文件，它会从此文件读取依赖版本,这确保了该库的每个使用者都能得到相同的依赖版本。
如果不存在 `composer.lock` 文件，它会从composer.json 文件读取依赖版本，并把其安装到 vendor 目录下。

如果没有 `composer.lock` 文件，composer 将在处理完依赖关系后创建它。

### composer require
添加一个包到composer.json文件,如果没有composer.json 就创建一个
```
# 该命令会安装两个不同的软件包
# vendor/package:2.* : 版本号以 2. 开头，后面跟着任何版本号。
# vendor/package2:dev-master : 使用 dev-master 分支。这意味着它将安装该分支的最新版本，通常是开发版本，不属于正式发布。
composer require "vendor/package:2.*" vendor/package2:dev-master
```

### composer update
获取依赖的最新版本
```
# 只更新这两个包vendor/package vendor/package2
composer update vendor/package vendor/package2

# 更新符合正则匹配的包
composer update "vendor/*"

# 更新依赖包到指定版本,需符合composer.json的约束
composer update --with vendor/package:2.0.1

# 通过 --with-all-dependencies 选项来允许所有依赖项进行升级
composer update "vendor/*" "vendor1/*" --with-all-dependencies
```

### composer remove
移除依赖包
```
# 移除这两个包
composer remove vendor/package vendor/package2
```

### composer reinstall
重装包,如果不小心改了包文件,可以使用重装命令恢复
```
# 重装两个包
composer reinstall acme/foo acme/bar

# 重装正则匹配的包
composer.phar reinstall "acme/*"
```

### composer check-platform-reqs
用于检查您的 PHP 和扩展版本是否符合已安装包的平台要求
```
--lock: 仅从锁定文件中检查要求，而不是从已安装的包中检查要求。
--no-dev: 不检查 require-dev 包要求。
-f: 格式
```
```
> composer check-platform-reqs
Checking platform requirements for packages in the vendor dir
ext-json      1.7.0   success
ext-libxml    7.3.4   success
ext-mbstring  7.3.4   success
ext-openssl   7.3.4   success
ext-simplexml 7.3.4   success
php           7.3.4   success
```

### composer global
global允许您全局运行其他命令，如install、remove、require,update

### composer search monolog
搜索依赖包
```
-N : 只搜包名
-O : 只搜作者
-t : 搜全称
```
```
λ composer search monolog
monolog/monolog                       Sends your logs to files, sockets, inboxes, databases and various web services
symfony/monolog-bundle                Symfony MonologBundle
symfony/monolog-bridge                Provides integration for Monolog with various Symfony components
...
```

### composer show / composer info
列出所有可用的包信息
```
λ composer show
defuse/php-encryption                v2.3.1    Secure PHP Encryption Library
laminas/laminas-diactoros            2.5.0     PSR HTTP Message implementations
laminas/laminas-zendframework-bridge 1.4.1     Alias legacy ZF class names to Laminas Project equivalents.
...
```
列出单个包的详情
```
λ composer show slim/slim
name     : slim/slim
descrip. : Slim is a PHP micro framework that helps you quickly write simple yet powerful web applications and APIs
keywords : api, framework, micro, router
versions : * 3.12.4
type     : library
license  : MIT License (MIT) (OSI approved) https://spdx.org/licenses/MIT.html#licenseText
homepage : https://slimframework.com
source   : [git] https://github.com/slimphp/Slim.git ce3cb65a06325fc9fe3d0223f2ae23113a767304
dist     : [zip] https://api.github.com/repos/slimphp/Slim/zipball/ce3cb65a06325fc9fe3d0223f2ae23113a767304 ce3cb65a06325fc9fe3d0223f2ae23113a767304
path     : D:\workspace\oauth\examples\vendor\slim\slim
names    : slim/slim, psr/http-message-implementation

support
issues : https://github.com/slimphp/Slim/issues
source : https://github.com/slimphp/Slim/tree/3.12.4

autoload
psr-4
Slim\ => Slim

requires
ext-json *
ext-libxml *
ext-simplexml *
nikic/fast-route ^1.0
php >=5.5.0
pimple/pimple ^3.0
psr/container ^1.0
psr/http-message ^1.0

requires (dev)
phpunit/phpunit ^4.0
squizlabs/php_codesniffer ^3.6.0

provides
psr/http-message-implementation 1.0
```

### composer outdated
列出所有安装包是否可以更新
```
λ composer outdated
Direct dependencies required in composer.json:
laminas/laminas-diactoros 2.5.0  2.14.0 PSR HTTP Message implementations
league/event              2.2.0  3.0.1  Event package
slim/slim                 3.12.4 4.9.0  Slim is a PHP micro framework that helps you quickly write simple yet po...

Transitive dependencies not required in composer.json:
psr/container             1.1.1  2.0.1  Common Container Interface (PHP FIG PSR-11)
```

### composer browse / composer home
浏览器直接打开这个包的github仓库
```
# 打开这个包的官网
composer browser -H slim/slim
# 打印slim的github仓库链接
composer browser -s slim/slim
```

### composer suggests
给你人生路上一点建议
```
λ composer suggests
lcobucci/jwt suggests:
 - lcobucci/clock: *

1 additional suggestions by transitive dependencies can be shown with --all
```

### composer fund
给出你所用的包的捐赠链接

### composer depends / why
composer depends 命令用于显示一个包的依赖关系树。如果你要删除一个包之前,可以用这个命令先看看它上面是不是有人
```
> composer depends psr/log -t

psr/log 1.1.4 Common interface for logging libraries
├──composer/composer 2.4.x-dev (requires psr/log ^1.0 || ^2.0 || ^3.0)
├──composer/composer dev-main (requires psr/log ^1.0 || ^2.0 || ^3.0)
├──composer/xdebug-handler 3.0.3 (requires psr/log ^1 || ^2 || ^3)
│  ├──composer/composer 2.4.x-dev (requires composer/xdebug-handler ^2.0.2 || ^3.0.3)
│  └──composer/composer dev-main (requires composer/xdebug-handler ^2.0.2 || ^3.0.3)
└──symfony/console v5.4.11 (conflicts psr/log >=3) (circular dependency aborted here)
```

### composer prohibits / why-not
告诉您哪些包在阻止你想安装的包,并给出理由

### composer validate
如果你手动修改composer.json,当准备提交composer.json文件之前,最好执行这条命令检查一下

### composer status
如果你手动修改过一个包,这个包安装来源是source,则可以用这个命令看你本地修改记录,相当于git status

### composer self-update / selfupdate
composer程序自更新
```
# 更新到指定版本
composer self-update 2.4.0-RC1
```

### composer config
修改当前项目或者全局的配置
```
-g : 修改全局配置文件
--unset: 移除配置
-l: 展示全部配置信息,如果加上-g,就显示全局的
--absolute: *-dir的配置返回绝对路径
--append: 追加一个镜像时,设置比较低的优先级
--source: 展示config从哪里加载的
```
```
# 添加一个测试foo到repositories
composer config repo.foo vcs https://github.com/foo/bar
# 添加一个阿里云镜像到repositories(阿里云镜像凉了)
composer config repo.packagist composer https://mirrors.aliyun.com/composer/

#效果如下
"repositories": {
    "packagist": {
        "type": "composer",
  		"url": "https://mirrors.aliyun.com/composer/"
	},
  	"foo": {
		"type": "vcs",
		"url": "https://github.com/foo/bar"
	}
}

# 修改extra配置
composer config extra.foo.bar value
composer config --json extra.foo.bar '{"baz": true, "qux": []}'
```

### composer create-project
创建项目/包,相当于git clone + composer install
```
composer create-project doctrine/orm path "2.2.*"
```

### composer dump-autoload
当修改了包里面的类名,或者增加删除文件之后,需要执行这个命令
```
# -o 选项是为了生产环境中的性能优化，
composer dump-autoload -o
# -a 选项则是为了开发环境中的重新生成类映射。
composer dump-autoload -a
```
### composer clear-cache / clearcache / cc
清理本地包缓存

### archive
从远程下载一个包,并打包成zip/tar压缩包
```
php composer.phar archive vendor/package 2.0.21 --format=zip
```
### run-script / run
你可以运行此命令来手动执行，只需要指定脚本的名称，可选的 `--no-dev` 参数允许你禁用开发者模式。
```
{
    "scripts": {
        "post-update-cmd": "MyVendor\\MyClass::postUpdate",
        "post-package-install": [
            "MyVendor\\MyClass::postPackageInstall"
        ],
        "post-install-cmd": [
            "MyVendor\\MyClass::warmCache",
            "phpunit -c app/"
        ]
    }
}
# 将会运行所有 post-install-cmd 事件下定义的脚本。
`composer run-script post-install-cmd` 
```

### diagnose
可以用来检查当前 Composer 环境是否符合最佳实践，包括 PHP 环境、Composer 配置等。

## audit
检查当前项目的依赖项是否存在已知的安全漏洞。

## help
使用 `help` 可以获取指定命令的帮助信息。
```
php composer.phar help install
```
