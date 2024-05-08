# Homestead PHP安装多版本扩展

### 通过php perl扩展库查找对应的php版本扩展压缩包进行下载

```bash
sudo -i
cd /home && mkdir extention
mkdir mongodb # 新建扩展包目录
sudo wget https://pecl.php.net/get/mongodb-1.15.0.tgz # 示例
```

### 编译安装
```bash
tar -zxvf mongodb-1.15.0.tgz # 解压
cd mongodb-1.15.0/  # whereis phpize || whereis php-config
/usr/bin/phpize7.3 # 构建(使用指定版本)
./configure --with-php-config=/usr/bin/php-config7.3 # 编译配置(使用指定版本)
make && make install
```

### 修改php配置文件
```bash
# extension=mongodb.so
vim /etc/php/7.3/fpm/php.ini
vim /etc/php/7.3/cli/php.ini
```

### 重启php服务
```bash
service php7.3-fpm restart
php7.3 -m # 查看扩展是否生效
```

### rabbitmq扩展安装注意事项
```bash
sudo apt-get update
sudo apt-get install librabbitmq-dev librabbitmq librabbitmq-tools
```

### 查看错误日志
```bash
tail -f /var/log/php7.3-fpm.log # php-fpm日志
tail -f /var/log/nginx/access.log # nginx日志
```