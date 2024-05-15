## Composer包开发
GitHub 创建一个名 package-name 新仓库，并克隆至本地。
```bash
git clone git@github.com:your-vendor-name/package-name.git
cd uploadfile
```
### 初始化项目，生成composer.json文件
```bash
$ composer init


  Welcome to the Composer config generator



This command will guide you through creating your composer.json config.

// 1. 输入项目命名空间
// 注意<vendor>/<name> 必须要符合 [a-z0-9_.-]+/[a-z0-9_.-]+
Package name (<vendor>/<name>) [dell/htdocs]: yourname/projectname

// 2. 项目描述
Description []: 这是一个测试

// 3. 输入作者信息，可以直接回车
Author [test <test@qq.com>, n to skip]:

// 4. 输入最低稳定版本，stable, RC, beta, alpha, dev
Minimum Stability []: dev

// 5. 输入项目类型，
Package Type (e.g. library, project, metapackage, composer-plugin) []: library

// 6. 输入授权类型
License []:
> Define your dependencies.

// 7. 输入依赖信息
Would you like to define your dependencies (require) interactively [yes]?

// 如果需要依赖，则输入要安装的依赖
Search for a package: php

// 输入版本号
Enter the version constraint to require (or leave blank to use the latest version): >=5.4.0

// 如需多个，则重复以上两个步骤

// 8. 是否需要require-dev，
Would you like to define your dev dependencies (require-dev) interactively [yes]?

// 操作同上
{
    "name": "your-vendor-name/package-name",
    "description": "A short description of what your package does",
    "type": "library",
    "require": {
        "php": ">=5.4"
    },
    "require-dev": {
        "php": ">=5.4"
    },
    "license": "MIT",
    "authors": [
        {
            "name": "test",
            "email": "test@qq.com"
        }
    ],
    "minimum-stability": "dev"
}

// 9. 是否生成composer.json
Do you confirm generation [yes]? yes
```

### 三、添加自动加载
```bash
"autoload": {
    "psr-4": {
        "Foo\\": "src/"
    }
}
```

### 四、构建项目
#### 4.1 新建package-name/src/Test.php
```bash
├─package-name               
│  ├─src                     
│  │  ├─Bar.php
│  └─composer.json
```
```bash
<?php

namespace Foo;  // 注意命名空间与 composer.json 中的一致

class Bar
{
    
}
```
#### 4.2 测试
* 4.2.1 终端下执行 composer install，这时会生成vendor目录，及其他文件
* 4.2.2 package-name/test/BarTest.php 验证测试

### 五、添加 README.md、LICENSE、.gitignore等文件，项目最终结构如下：我的包GitHub地址
```bash
├─package-name                扩展包根目录
│  ├─src                    扩展包代码目录
│  │  ├─Bar.php
│  ├─test                   测试目录
│  │  ├─BarTest.php
│  ├─.gitignore
│  ├─composer.json
│  ├─LICENSE
│  └─README.md
```

### 六、推送到 GitHub
```bash
git add .
git commit -m 'init'
git tag v1.0.0 // 记住打一个版本号
git push origin master
git push v1.0.0
```

### 七、将 GitHub 上的包提交到 Packagist
* 首先要在 Packagist 上注册账号并登录（可以用 GitHub 直接登录）
* 点击顶部导航条中的 Summit 按钮
* 在输入框中输入 GitHub 上的刚才包地址，如：https://github.com/your-vendor-name/package-name
* 然后点击 Check 按钮 Packagist 会去检测此仓库地址的代码是否符合 Composer 的 Package 包的要求

### 八、设置 composer 包自动更新
* 访问 https://packagist.org/profile/ 在页面上点击「Show API Token」按钮，获取你的 Packagist API Token
* 在需要设置发布和自动更新的 GitHub 仓库下，通过「Settings」->「Webhooks」添加一个新的 WebHook，配置参考如下：
```bash
URL：https://packagist.org/api/github?username=YOUR_PACKAGIST_USERNAME
Content Type: application/json，
Secret: your Packagist API Token
```
* 其中：YOUR_PACKAGIST_USERNAME 需要替换为你在 Packagist.org 上的用户名，Secret 为上文中获得到的 Packagist API Token。 事件勾选 Just the push event. 即可。
* 配置完成后，通过向仓库推送代码。即可触发自动更新到 Packagist.org。Packagist平台上的自动更新提示将变为以下内容:This package is auto-updated.