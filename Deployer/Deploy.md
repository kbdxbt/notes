## 在windows（WSL2）使用Deployer部署laravel

### 准备工作
开启适用于Linux的Windows子系统和虚拟机功能

### 安装Ubuntu系统
进入Microsoft Store商店，搜索Ubuntu系统版本（例如：Ubuntu 20.04 LTS）进行安装，

### 初始化Ubuntu配置：账号和密码
账号设置为：deployer
ps: 通过cmd执行 ubuntu2004 config --default-user root 可以切换默认登录账号

```bash
sudo usermod -aG www-data deployer
sudo chown deployer:www-data /var/www/html
```

### 更换软件源
清华大学开源软件镜像站：https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/
```bash
sudo cp /etc/apt/sources.list /etc/apt/sources_init.list
sudo vim /etc/apt/sources.list # dG 全选删除
```

### 安装必须软件
ssh
```bash
sudo apt-get update
sudo apt-get install ssh
sudo /etc/init.d/ssh start
```
开启ssh密码登录
```bash
sudo vim /etc/ssh/sshd_config # 修改 PasswordAuthentication yes
sudo service ssh reload
```
php
```bash
sudo add-apt-repository ppa:ondrej/php
sudo apt update
sudo apt install php8.1 php8.1-common php8.1-cli php8.1-fpm php8.1-mysql php8.1-zip php8.1-gd php8.1-mbstring php8.1-curl php8.1-xml
php -v
```
composer
```bash
curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/usr/local/bin --filename=composer
composer --version
composer config -g repo.packagist composer https://mirrors.cloud.tencent.com/composer/
```
git
```bash
sudo apt install git
git --version
```
nginx
```bash
sudo apt install nginx
sudo apt-get install acl
/etc/init.d/nginx start
```
mysql
```bash
sudo apt install mysql-server mysql-client
/etc/init.d/mysql start
mysql -u root -p
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY ''; #设置root密码
FLUSH PRIVILEGES;
```

### deployer配置
服务端
```bash
visudo
# 在最后加入
deployer ALL=(ALL) NOPASSWD: ALL
# 保存并退出
```
客户端
```bash
ssh-keygen -t rsa -b 4096 -f  ~/.ssh/deployerkey
ssh-copy-id -i  ~/.ssh/deployerkey.pub deployer@127.0.0.1 # 请填写服务器 IP
ssh deployer@127.0.0.1 -i ~/.ssh/deployerkey
```

### 执行deploy
```bash
vendor/bin/dep deploy -vvv
```
